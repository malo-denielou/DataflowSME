# Tutorial for Cloud Dataflow

This is a collection of tutorial-style Dataflow exercises based on the [Dataflow
gaming
example](https://github.com/GoogleCloudPlatform/DataflowJavaSDK-examples/blob/master/src/main/java8/com/google/cloud/dataflow/examples/complete/game/README.md)
and inspired by the [Beam tutorial](https://github.com/eljefe6a/beamexample).

In the gaming scenario, many users play, as members of different teams, over the
course of a day, and their events are logged for processing.

The exercises either read the batch data from CSV files on GCS or the streaming
data from a [PubSub](https://cloud.google.com/pubsub/) topic (generated by the
included `Injector` program). All exercises write their output to BigQuery.

## Set up your environment

### Tools

1.  Install [Java 8](https://java.com/fr/download/)
1.  Install an IDE such as [Eclipse](https://eclipse.org/downloads/) or [IntelliJ](https://www.jetbrains.com/idea/download/) (optional)
1.  Install Maven (for [windows](https://maven.apache.org/guides/getting-started/windows-prerequisites.html), 
    [mac](http://tostring.me/151/installing-maven-on-os-x/), [linux](http://maven.apache.org/install.html))
1.  Google [Cloud SDK](https://cloud.google.com/sdk/)
1.  To test your installation, open a terminal window and type:
    *   java -version
    *   mvn --version
    *   gcloud --version

### Google Cloud

1.  Go to https://cloud.google.com/console.
1.  Enable billing and create a project.
1.  Enable Google Dataflow API and BigQuery API.
1.  Create a GCS bucket in your project as a staging location.
1.  Create a BigQuery dataset in your project.


### Download the code
1.  Run `git clone https://github.com/mdvorsky/DataflowSME.git`
1.  Run `cd DataflowSME`

## Exercise 0 (prework)

Use the provided Dataflow pipeline to import the input events from GCS to
BigQuery.

How many parse errors did you encounter? How many unique teams are present in
the dataset?

1.  Run `mvn compile exec:java
    -Dexec.mainClass=com.google.cloud.dataflow.tutorials.game.Exercise0
    -Dexec.args="--project=YOUR-PROJECT
    --stagingLocation=gs://YOUR-STAGING-BUCKET
    --runner=BlockingDataflowPipelineRunner --outputDataset=YOUR-DATASET
    --outputTableName=events
    --input=gs://dataflow-samples/game/gaming_data1.csv"`
1.  Navigate to the Dataflow UI (the link is printed in the terminal, look for
    `"To access the Dataflow monitoring console, please navigate to ..."`).
1.  Once the pipeline finishes, check the value of `ParseGameEvent/ParseErrors`
    aggregator on the UI (look for `Custom counters` section in the `Summary`
    tab).
1.  Check the number of distinct teams in the created BigQuery table. `bq query
    --project_id=YOUR-PROJECT 'select count(distinct team) from
    YOUR-DATASET.events;'`

## Exercise 1

Use Dataflow to calculate per-user scores and write them to BigQuery.

What is the total score of the user 'user1_AmethystKoala'?

1.  Modify
    src/main/java8/com/google/cloud/dataflow/tutorials/game/Exercise1.java
1.  Run `mvn compile exec:java
    -Dexec.mainClass=com.google.cloud.dataflow.tutorials.game.Exercise1
    -Dexec.args="--project=YOUR-PROJECT
    --stagingLocation=gs://YOUR-STAGING-BUCKET
    --runner=BlockingDataflowPipelineRunner --outputDataset=YOUR-DATASET
    --outputTableName=user_scores
    --input=gs://dataflow-samples/game/gaming_data1.csv"`
1.  Once the pipeline finishes successfully check the score for
    'user1_AmethystKoala': `bq query --project_id=YOUR-PROJECT 'select
    total_score from YOUR-DATASET.user_scores where user =
    'user1_AmethystKoala';'`

## Exercise 2

Use Dataflow to calculate per-hour team scores and write them to BigQuery.

What was the total score of 'AmethystKoala' at '2015-11-17 13:00:00 UTC'?

## Exercise 3

Use Dataflow to create a streaming per-hour BigQuery LeaderBoard of team scores
for events coming through PubSub. Some of the logged game events may be
late-arriving, if users play on mobile devices and go transiently offline for a
period.

Which teams are in the lead?

## Exercise 4

Use Dataflow to eliminate "robot users" (users with higher click rate than
regular users) from team scores.
