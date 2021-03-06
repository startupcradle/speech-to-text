---

copyright:
  years: 2015, 2017
lastupdated: "2017-08-13"

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

# Basic usage
{: #basic}

1.  <span style="color:#003F69">My audio file is larger than 100 MB, which is too big for the service to accept. Is there a way that I can process the file?</span>

    One solution is to manually divide the file into smaller pieces. Alternatively, although you risk degrading the quality of the audio, you can compress the file by using tools such as Sound eXchange ([sox.sourceforge.net ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://sox.sourceforge.net){: new_window}) or FFmpeg ([www.ffmpeg.org ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ffmpeg.org){: new_window}). For information about encoding your audio efficiently, see [Data limits and compression](/docs/services/speech-to-text/input.html#limits).

1.  <span style="color:#003F69">I passed a one-hour long audio file to the service but did not see results until the full transcription completed. How can I see my results sooner?</span>

    Use HTTP sessions or the WebSocket interface, and set the `interim_results` parameter to `true`. By default, the service returns results only when it detects long silences in the audio. See [Interim results](/docs/services/speech-to-text/output.html#interim).

1.  <span style="color:#003F69">My request times out with an HTTP status code of 400 and the message `No speech detected for 30s`. What can I do?</span>

    By default, the service responds with an inactivity timeout if it detects 30 seconds of continuous silence or non-speech activity at any time. To prevent the timeout, set the `inactivity_timeout` parameter for your request to a value greater than 30 seconds; set the parameter to `-1` to specify an inactivity timeout of infinity. See [Timeouts](/docs/services/speech-to-text/input.html#timeouts).

1.  <span style="color:#003F69">My audio includes pauses of varying lengths. How does the service respond to them?</span>

    The service transcribes an entire audio stream until either the stream ends or a timeout occurs, whichever comes first. If your input includes pauses, transcription results can include multiple `transcript` elements to indicate phrases separated by the pauses. Concatenate the `transcript` elements to assemble the complete transcription of the audio stream.

    All methods that initiate recognition requests previously included a `continuous` parameter that you could set to `true` to avoid having the service stop transcription at the first end of speech incident, typically silence. The service no longer stops transcription at pauses, and the parameter has been removed from all methods.

1.  <span style="color:#003F69">Can I change the time or pause interval that determines when the end of a phrase occurs?</span>

    No. This capability is not currently available with the service.

1.  <span style="color:#003F69">My output includes multiple transcriptions of my content. Which one should I use?</span>

    Use the transcription for which the JSON `final` attribute is `true`; ignore the intermediate results for which the attribute is `false`.

1.  <span style="color:#003F69">What is {{site.data.keyword.IBM_notm}}'s data-storage policy for audio that I submit to the service?</span>

    By default, {{site.data.keyword.IBM_notm}} logs each request to the service and its results; {{site.data.keyword.IBM_notm}} uses the data only to improve the service's base speech models for future users. You can prevent {{site.data.keyword.IBM_notm}} from storing your data by setting the `X-Watson-Learning-Opt-Out` request header to `true` for all requests. When you use the header to opt out of request logging, {{site.data.keyword.IBM_notm}} stores none of your data; your data exists within {{site.data.keyword.watson}} only while it is in transit (while the service is processing your request). In either case, your data is always encrypted both in motion and at rest. For more information, see [Authentication tokens and request logging](/docs/services/speech-to-text/input.html#common).
