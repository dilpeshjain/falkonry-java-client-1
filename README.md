[![Falkonry Logo](http://static1.squarespace.com/static/55a7df64e4b09f03368a7a78/t/569c6441ab281050fe32c18a/1453089858079/15-logo-transparent-h.png?format=500w)](http://falkonry.com/)

Falkonry Java Client to access [Falkonry Condition Prediction](falkonry.com) APIs

[Releases](https://github.com/Falkonry/falkonry-java-client/releases)

## Installation

Maven install
```
<dependency>
  <groupId>com.falkonry</groupId>
  <artifactId>client</artifactId>
  <version>0.1.7</version>
</dependency>
```

## Features

    * Create Eventbuffer
    * Retrieve Eventbuffers
    * Create Pipeline
    * Retrieve Pipelines
    * Add data to Eventbuffer (csv/json, stream)
    * Add facts to Pipeline (csv/json, stream)

## Quick Start

    * Get auth token from the Falkonry Service UI.
    * Read below examples for integration with various data formats.

## Examples

#### Setup Eventbuffer for narrow/historian style data from a single entity

Data:
```
    {"time" :"2016-03-01 01:01:01", "tag" : "signal1", "value" : 3.4}
    {"time" :"2016-03-01 01:01:02", "tag" : "signal2", "value" : 9.3}

    or

    time, tag, value
    2016-03-01 01:01:01, signal1, 3.4
    2016-03-01 01:01:02, signal2, 9.3

```

Usage:
```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.Eventbuffer;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Timezone timezone = new Timezone();
    timezone.setZone("GMT");
    timezone.setOffset(0);

    Eventbuffer eb = new Eventbuffer()
        .setName("Eventbuffer_name")  //name of the eventbuffer
        .setTimeIdentifier("time")    //property that identifies time in the data
        .setTimeFormat("iso_8601")    //format of the time in the data
        .setTimezone(timezone)        //output data will be generated using timezone
        .setValueColumn("value")      //property that identifies value of the signal in the data
        .setSignalsTagField("tag");   //property that identifies signal tag in the data

    //create eventbuffer
    Eventbuffer eventbuffer = falkonry.createEventbuffer(eb);

    //Add data to eventbuffer
    String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1\", \"value\" : 3.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2\", \"value\" : 9.3}";
    InputStatus inputStatus = falkonry.addInput(eventbuffer.getId(), data, options);

```

#### Setup Eventbuffer for narrow/historian style data from multiple entities

Data:
```
    {"time" :"2016-03-01 01:01:01", "tag" : "signal1_entity1", "value" : 3.4}
    {"time" :"2016-03-01 01:01:01", "tag" : "signal2_entity1", "value" : 1.4}
    {"time" :"2016-03-01 01:01:02", "tag" : "signal1_entity2", "value" : 9.3}
    {"time" :"2016-03-01 01:01:02", "tag" : "signal2_entity2", "value" : 4.3}

    or

    time, tag, value
    2016-03-01 01:01:01, signal1_entity1, 3.4
    2016-03-01 01:01:01, signal2_entity1, 1.4
    2016-03-01 01:01:02, signal1_entity2, 9.3
    2016-03-01 01:01:02, signal2_entity2, 4.3
```

Usage:
```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.Eventbuffer;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Eventbuffer eb = new Eventbuffer()
        .setName("Eventbuffer_name")  //name of the eventbuffer
        .setTimeIdentifier("time")    //property that identifies time in the data
        .setTimeFormat("iso_8601")    //format of the time in the data
        .setValueColumn("value")      //property that identifies value of the signal in the data
        .setSignalsDelimiter("_")     //delimiter used to concat system id and signal name to create signal tag
        .setSignalsLocation("prefix") //part of the tag that identifies signal name
        .setSignalsTagField("tag");   //property that identifies signal tag in the data

    //create eventbuffer
    Eventbuffer eventbuffer = falkonry.createEventbuffer(eb);

    //Add data to eventbuffer
    String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1_entity1\", \"value\" : 3.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal2_entity1\", \"value\" : 1.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal1_entity1\", \"value\" : 9.3}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2_entity2\", \"value\" : 4.3}";
    InputStatus inputStatus = falkonry.addInput(eventbuffer.getId(), data, options);

```

#### Setup Eventbuffer for wide style data from a single entity

Data:
```
    {"time":1467729675422, "signal1":41.11, "signal2":82.34, "signal3":74.63, "signal4":4.8}
    {"time":1467729668919, "signal1":78.11, "signal2":2.33, "signal3":4.6, "signal4":9.8}

    or

    time, signal1, signal2, signal3, signal4
    1467729675422, 41.11, 62.34, 77.63, 4.8
    1467729675445, 43.91, 82.64, 73.63, 3.8
```

Usage:
```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.Eventbuffer;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Eventbuffer eb = new Eventbuffer()
        .setName("Eventbuffer_name")  //name of the eventbuffer
        .setTimeIdentifier("time")    //property that identifies time in the data
        .setTimeFormat("millis");     //format of the time in the data

    //create eventbuffer
    Eventbuffer eventbuffer = falkonry.createEventbuffer(eb);

    //Add data to eventbuffer
    String data = "{\"time\":1467729675422,\"signal1\":41.11,\"signal2\":82.34,\"signal3\":74.63,\"signal4\":4.8}" + "\n"
        + "{\"time\":1467729668919,\"signal1\":78.11,\"signal2\":2.33,\"signal3\":4.6,\"signal4\":9.8}";
    InputStatus inputStatus = falkonry.addInput(eventbuffer.getId(), data, options);
```

#### Setup Eventbuffer for wide style data from multiple entities

Data:
```
    {"time":1467729675422, "entities": "entity1", "signal1":41.11, "signal2":82.34, "signal3":74.63, "signal4":4.8}
    {"time":1467729668919, "entities": "entity2", "signal1":78.11, "signal2":2.33, "signal3":4.6, "signal4":9.8}

    or

    time, entities, signal1, signal2, signal3, signal4
    1467729675422, entity1, 41.11, 62.34, 77.63, 4.8
    1467729675445, entity1, 43.91, 82.64, 73.63, 3.8
```

Usage:
```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.Eventbuffer;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Eventbuffer eb = new Eventbuffer()
        .setName("Eventbuffer_name")  //name of the eventbuffer
        .setTimeIdentifier("time")    //property that identifies time in the data
        .setTimeFormat("millis")      //format of the time in the data
        .setEntityIdentifier("entities"); //property that identifies system id in the data.

    //create eventbuffer
    Eventbuffer eventbuffer = falkonry.createEventbuffer(eb);

    //Add data to eventbuffer
    String data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
        + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
        + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";
    InputStatus inputStatus = falkonry.addInput(eventbuffer.getId(), data, options);
```

#### Get an Eventbuffer

```java
    import com.falkonry.client.Falkonry;

    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

    eventbuffer = falkonry.getUpdatedEventbuffer("eventbuffer_id"); //eventbuffer's id
```

#### Add json data from a stream to an Eventbuffer
    
```java
    import com.falkonry.client.Falkonry
    import org.apache.commons.io.FileUtils;

    Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Map<String, String> options = new HashMap<String, String>();

    File file = new File("tmp/data.json");      
    ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

    InputStatus inputStatus = falkonry.addInputStream(eventbuffer.getId(),byteArrayInputStream,options);
```

#### Add csv data from a stream to an Eventbuffer
    
```java
    import com.falkonry.client.Falkonry
    import org.apache.commons.io.FileUtils;

    Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    Map<String, String> options = new HashMap<String, String>();

    File file = new File("tmp/data.csv");     
    ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

    InputStatus inputStatus = falkonry.addInputStream(eventbuffer.getId(),byteArrayInputStream,options);
```

#### Setup Pipeline from Eventbuffer
    
```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.*;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

    //prepare signals to be used from eventbuffer
    List<Signal> signals = new ArrayList<Signal>();
    signals.add(
        new Signal()
            .setName("current")                    //signal present in eventbuffer
            .setValueType(
                new ValueType().setType("Numeric") //type of the signal. Numeric or Categorical
            )
            .setEventType(
                new EventType().setType("Samples") //nature of the signal. Samples or Occurrences
            )
    );
    signals.add(
        new Signal()
            .setName("vibration")
            .setValueType(
                new ValueType().setType("Numeric")
            )
            .setEventType(
                new EventType().setType("Samples")
            )
    );
    signals.add(
        new Signal()
            .setName("state")
            .setValueType(
                new ValueType().setType("Categorical")
            )
            .setEventType(
                new EventType().setType("Samples")
            )
    );

    //prepare assessments
    List<String> inputList = new ArrayList<String>(); //signals to be added in the assessment
    inputList.add("current");
    inputList.add("vibration");
    inputList.add("state");

    List<Assessment> assessmentList = new ArrayList<Assessment>();
    Assessment assessment = new Assessment()
                    .setName("Health")               //name of the assessment
                    .addSignals(assessment_signals); //signals to added in this assessment
    assessmentList.add(assessment);

    //prepare interval
    Interval interval = new Interval();
        interval.setDuration("PT1S");               //lower bound to be set
                            
    //create pipeline
    Pipeline pipeline = new Pipeline()
                    .setName("Motor Health")             //name of the pipeline
                    .setEventbuffer(eventbuffer.getId()) //eventbuffer's id
                    .setInputSignals(signals)            //list of signals
                    .setAssessmentList(assessments)      //list of assessments
                    .setInterval(interval);              //interval configuration

    Pipeline createdPipeline = falkonry.createPipeline(pipeline);
```

#### To get all Pipelines
    
```java
    import com.falkonry.client.Falkonry;

    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

    List<Pipeline> pipelines = falkonry.getPipelines();
```

#### Add facts data (json format) to a Pipeline

```java
    import com.falkonry.client.Falkonry;

    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

    String data = "{\"time\" : \"2011-03-26T12:00:00Z\", \"entities\" : \"entity1\", \"end\" : \"2012-06-01T00:00:00Z\", \"Health\" : \"Normal\"}";
    String response = falkonry.addfacts(pipeline.getId(),data, options);
```

#### To add facts data (csv format) to a Pipeline

```java
    import com.falkonry.client.Falkonry;

    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

    String data = "time,end,car,Health\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,IL9753,Normal\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,HI3821,Normal";
    String response = falkonry.addFacts(pipeline.getId(),data, options);
```

#### Add facts data (json format) from a stream to a Pipeline
    
```java
    import com.falkonry.client.Falkonry;
    import org.apache.commons.io.FileUtils;

    Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    File file = new File("res/factsData.json");      
    ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));
    String response = falkonry.addFactsStream(pipeline.getId(),byteArrayInputStream, options);
```

#### Add facts data (csv format) from a stream to a Pipeline
    
```java
    import com.falkonry.client.Falkonry;
    import org.apache.commons.io.FileUtils;

    Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    File file = new File("res/factsData.csv");      
    ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));
    String response = falkonry.addFactsStream(pipeline.getId(),byteArrayInputStream, options);
```

## Docs

    [Falkonry APIs](https://sandbox.falkonry.ai/api)
     
## Tests

  To run the test suite, first install the dependencies, then run tests from the test folder.
  

## License

  Available under [MIT License](LICENSE)
