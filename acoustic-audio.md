---

copyright:
  years: 2017
lastupdated: "2017-10-02"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Managing audio resources
{: #manageAudio}

In addition to the `POST /v1/acoustic_customizations/{customization_id}/audio/{audio_name}` method, which is used to add an audio resource to a custom acoustic model (see [Add audio to the custom acoustic model](/docs/services/speech-to-text/acoustic-create.html#addAudio)), the customization interface includes methods for listing and deleting audio resources for a custom acoustic model.

## Listing audio resources for a custom acoustic model
{: #listAudio}

The customization interface provides two methods for listing information about the audio resources of a custom acoustic model:

-   The `GET /v1/acoustic_customizations/{customization_id}/audio` method lists information about all audio resources that have been added to a custom model.
-   The `GET /v1/acoustic_customizations/{customization_id}/audio/{audio_name}` method lists information about a specified audio resource for a custom model.

Both methods return the `name` of the audio resource plus the following additional information:

-   `duration`: The length in seconds of the audio; for an archive file, this is the accumulated duration of all of the audio files contained in the archive.
-   `details`: The type of the resource, `audio` or `archive`. For an audio file, the details include the `codec` and `frequency` of the audio; for an archive file, they include its `compression` type.

Additionally, listing all audio resource for a model returns the `total_minutes_of_audio` summed over all of the valid audio resources for the model. You can use this value to determine whether the custom model has enough or too much audio to begin training.

The methods also list the status of the data, which is important for checking the service's analysis of audio files in response to a request to add them to a custom model:

-   `ok` indicates that the service has successfully analyzed the audio data. The data can be used to train the custom model.
-   `being_processed` indicates that the service is still analyzing the audio data. The service cannot accept requests to add new audio or to train the custom model until its analysis is complete.
-   `invalid` indicates that the audio data is not valid for training the model (possibly because it has the wrong format or sampling rate, or because it is corrupted).

### Example requests and responses
{: #listExample}

The following example lists all audio resources for the custom acoustic model with the specified customization ID:

```bash
curl -X GET -u {username}:{password}
"https://stream.watsonplatform.net/speech-to-text/api/v1/acoustic_customizations/{customization_id}/audio"
```
{: pre}

In the following example, three audio resources have been added to the custom acoustic model. The service has successfully analyzed `audio1` and `audio2`; the service is currently analyzing `audio3`.

```javascript
{
  "total_minutes_of_audio": 355.1147255897521973,
  "audio": [
    {
      "duration": 3.8428750038146973,
      "name": "audio1",
      "details": {
        "codec": "pcm_s16le",
        "type": "audio",
        "frequency": 16000
      }
      "status": "ok"
    },
    {
      "duration": 351.2718505859375,
      "name": "audio2",
      "details": {
        "type": "archive",
        "compression": "zip"
      },
      "status": "ok"
    },
    {
      "name": "audio3",
      "duration": 0,
      "details": {},
      "status": "being_processed"
    }
  ]
}
```
{: codeblock}

The following example returns information about the audio resource named `audio2` (which is a **.zip** archive file that contains multiple files) for the custom acoustic model with the specified customization ID. As shown, querying information about an archive-type resource also provides information about its constituent files.

```bash
curl -X GET -u {username}:{password}
https://stream.watsonplatform.net/speech-to-text/api/v1/acoustic_customizations/{customization_id}/audio/audio2
```
{: pre}

```javascript
{
  "container": {
    "duration": 351.2718505859375,
    "name": "audio2",
    "details": {
      "type": "archive",
      "compression": "zip"
    },
    "status": "ok"
  },
  "audio": [
    {
      "duration": 11.760937690734863,
      "name": "arl001.wav",
      "details": {
        "codec": "pcm_s16le",
        "type": "audio",
        "frequency": 16000
      },
      "status": "ok"
    },
    {
      "duration": 2.9024999141693115,
      "name": "arl002.wav",
      "details": {
        "codec": "pcm_s16le",
        "type": "audio",
        "frequency": 16000
      },
      "status": "ok"
    },
    . . .
  ]
}
```
{: codeblock}

## Deleting an audio resource from a custom acoustic model
{: #deleteAudio}

Use the `DELETE /v1/acoustic_customizations/{customization_id}/audio/{audio_name}` method to remove an existing audio resource from a custom acoustic model. When deleting an archive-type audio resource, the service removes the entire archive of files. The current interface does not allow deletion of individual files from an archive resource.

Removing an audio resource does not affect the custom model until you train the model on its updated data by using the `POST /v1/acoustic_customizations/{customization_id}/train` method. If you successfully trained the model on the resource, until you retrain the model, the existing audio data continues to be used for speech recognition.

### Example request
{: #deleteExample}

The following method deletes the audio resource named `audio3` from the custom model with the specified customization ID:

```bash
curl -X DELETE -u {username}:{password}
"https://stream.watsonplatform.net/speech-to-text/api/v1/acoustic_customizations/{customization_id}/audio/audio3"
```
{: pre}
