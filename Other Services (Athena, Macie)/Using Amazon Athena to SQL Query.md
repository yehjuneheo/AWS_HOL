#### This lab covers about using Amazon Athena to SQL query from a public database.

## Using Amazon Athena to SQL Query
* In this lab I will use Athena to query the open street map database to locate local animal vet facilities for the animals for life mobile teams.

* First I navigated to the Athena console and launched the query editor.
![unnamed - 2024-06-21T012012 384](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b2df397a-28f4-4657-8fbf-d8faea2ca97d)

* Before I run any queries, I need to set up a query result location in S3. So I created a new S3 bucket with block all public access unchecked.
* Then I went to edit settings and added the bucket to be the query result location.
![unnamed - 2024-06-21T012016 169](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b6430bf1-b335-462b-9daa-64ac1badb050)

* Then I created the database usign the query editor and selected it.
![unnamed - 2024-06-21T012021 035](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aac60022-7083-45a7-913c-34ced956020b)

* Then I opened a new query and copy and pasted the commands from this file: https://learn-cantrill-labs.s3.amazonaws.com/awscoursedemos/0051-aws-mixed-athena/lesson_commands.txt
* The command is used to just create the schema for the data that will be stored.
![unnamed - 2024-06-21T012025 307](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3ce1ea95-5fab-4c2b-adbe-9d8ea4372176)

* Then I ran a simple query to test: Select * from planet LIMIT 100
![unnamed - 2024-06-21T012038 525](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a671cc15-32ec-489e-92ad-a50c0a99eeba)
* I can see that the data has been retrieved successfully following the schema I have provided.
