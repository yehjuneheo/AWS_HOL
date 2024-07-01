#### In this lab, I am going to build a serverless application that uses web identity federation using AWS Cognito.

## Implementing Web Identity Federation
* In this lab, I am going to build a serverless application that uses web identity federation using AWS Cognito.

* First I used the Cloudformation template provided by learn.cantrill.io to automate the building of some necessary resources.
![unnamed (11)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/de06fcee-b15d-4aed-9816-e0c6b30e0873)

* The template generated two buckets, one for static hosting and another private bucket that contains images.
* It also created a CloudFront distribution with a distribution domain name.
![unnamed (12)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/24546b21-acf7-4a05-ae45-7fac955acd50)

* Then I created OAuth 2.0 credentials to access Google APIs using the distribution domain name as the authorized javascript origins.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/64809ec9-7919-4c0e-a550-67ed46ca2bd4)
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e0d0c0d2-e5fc-410c-9cc7-3ff8e08714c1)

* Then I navigated to the AWS Cognito console to create a new identity pool.
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/15450e95-8b49-4369-92da-3f00db975c80)

* I entered in the OAuth Client ID and created the identity pool, and copied down the identity pool ID
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7945a7bc-ca6d-41a8-b209-4404cef5a26b)

* Then I navigated to the IAM console and attached the PrivatePachesPermissions policy that allows access to the private bucket to the Cognito IAM Role.

* Then I updated the index.html to use my OAuth client ID
````
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>PetIDF Demo</title>
    <meta name="author" content="acantril">
    <meta name="google-signin-scope" content="profile email">
    <meta name="referrer" content="strict-origin-when-cross-origin">
    <script src="https://sdk.amazonaws.com/js/aws-sdk-2.2.19.min.js"></script>
    <script src="scripts.js"></script>
    <script src="https://accounts.google.com/gsi/client" async defer></script>
  </head>
  <body>


    <div id="g_id_onload"
         data-client_id="REPLACE_ME_GOOGLE_APP_CLIENT_ID"
         data-context="signin"
         data-ux_mode="popup"
         data-callback="onSignIn"
         data-auto_prompt="false">
    </div>
   
    <div class="g_id_signin" data-type="standard"></div>
    <p />
    <div id="viewer"></div>
    <div id="output"></div>
   
  </body>
</html>
````

* Then I also updated the javascript file to use my credentials:
````
function onSignIn(googleToken) {
  // Google have OK'd the sign-in
  // pass the token into our web app
  credentialExchange(googleToken);
}


function credentialExchange(googleToken) {
  // Create a decoded version of the token so we can print things out
  console.log("Creating decoded token...");
  const googleTokenDecoded = parseJwt(googleToken.credential);
 
  // Output some details onto the browser console to show the token working
  console.log("ID: " + googleTokenDecoded.sub);
  console.log('Full Name: ' + googleTokenDecoded.name);
  console.log("Email: " + googleTokenDecoded.email);
 
  if (googleTokenDecoded['sub']) {
   
    // We can't access anything in AWS with a google token...
    // ... so we need to exchange it using Cognito for AWS credentials
    console.log("Exchanging Google Token for AWS credentials...");
    AWS.config.region = 'us-east-1';
    AWS.config.credentials = new AWS.CognitoIdentityCredentials({
      IdentityPoolId: 'REPLACE_ME_COGNITO_IDENTITY_POOL_ID', // MAKE SURE YOU REPLACE THIS
      Logins: {
        'accounts.google.com': googleToken.credential
      }
    });


    // Now lets obtain the credentials we just swapped
    AWS.config.credentials.get(function(err) {
      if (!err) {
        console.log('Exchanged to Cognito Identity Id: ' + AWS.config.credentials.identityId);
        // if we are here, things are working as they should...
        // ... now lets call a function to access images, generate signed URL's and display
        accessImages();
      } else {
        // if we are here, bad things have happened, so we should error.
        document.getElementById('output').innerHTML = "<b>YOU ARE NOT AUTHORISED TO QUERY AWS!</b>";
        console.log('ERROR: ' + err);
      }
    });


  } else {
    console.log('User not logged in!');
  }
}


function accessImages() {
 
  // Using the temp AWS Credentials, lets connect to S3
  console.log("Creating Session to S3...");
  var s3 = new AWS.S3();
  var params = {
    Bucket: "REPLACE_ME_NAME_OF_PATCHES_PRIVATE_BUCKET" // MAKE SURE YOU REPLACE THIS
  };


  // If we are here, things are going well, lets list all of the objects in the bucket
  s3.listObjects(params, function(err, data) {
    console.log("Listing objects in patchesprivate bucket...");
    if (err) {
      document.getElementById('output').innerHTML = "<b>YOU ARE NOT AUTHORISED TO QUERY AWS!</b>";
      console.log(err, err.stack);
    } else {
      console.log('AWS response:');
      console.log(data);
      var href = this.request.httpRequest.endpoint.href;
      var bucketUrl = href + data.Name + '/';
     
      // for all of the images in the bucket, we need to generate a signedURL for the object
      var photos = data.Contents.map(function(photo) {
        var photoKey = photo.Key;
       
        console.log("Generating signedURL for : " + photoKey);
        var url = s3.getSignedUrl ('getObject', {
          Bucket: data.Name,
          Key: photoKey
        })


        var photoUrl = bucketUrl + encodeURIComponent(photoKey);
        return getHtml([
          '<span>',
            '<div>',
              '<br/>',
              '<a href="' + url + '" target="_blank"><img style="width:224px;height:224px;" src="' + url + '"/></a>',
            '</div>',
            '<div>',
              '<span>',
              '</span>',
            '</div>',
          '</span>',
        ]);
      });


      // let's take those signedURL's, create a HTML page, and display it in the web browser
      var htmlTemplate = [ '<div>',   getHtml(photos), '</div>']
      console.log("Creating and returning html...")
      document.getElementById('viewer').innerHTML = getHtml(htmlTemplate);
    }    


  });
}


// A utility function to create HTML.
function getHtml(template) {
  return template.join('\n');
}


// A utility function to decode the google token
function parseJwt(token) {
  var base64Url = token.split('.')[1];
  var base64 = base64Url.replace('-', '+').replace('_', '/');
  var plain_token = JSON.parse(window.atob(base64));
  return plain_token;
};
````

* Then I uploaded the updated files and navigated to the distribution domain name, which displayed the sign in button for google.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9adaad11-067d-459e-8f33-5b6b146ce636)

* When I sign in, I can see the images being fetched from the private bucket.
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b71196a0-c610-4894-bd0b-8593a16fe732)
