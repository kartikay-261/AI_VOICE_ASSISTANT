# AI_VOICE_ASSISTANT

![alt text](https://github.com/kartikay-261/AI_VOICE_ASSISTANT/blob/main/AI%20voice%20assistant%20(1).png)
The above diagram shows a schematic view of the pipeline that I had in mind however due to constraints from the Google Cloud Personal account my cloud bucket exceeded quota and I had to shift the storage space to my local storage. I planned on developing an ETL pipeline to execute this however I had to compromise with an E2E python pipeline for this project. Given a company’s resources of AWS or GCP this could be changed to a cloud-based project orchestrated by Kubernetes.
The pipeline begins with a Gradio interface taking in the audio file and the required parameters as specified by the assignment PDF (GENDER, PITCH, RATE, VOLUME). These parameters and the audio are passed on to the pipeline. The pipeline has the following four processes.
1. Audio filtering step: The passed-on audio sample is first resampled to 16k Hz accepted by our library Silerio-VAD. We get the timestamps for voice activity from this function and trim the parts with no voice activity. This audio is then used for the STT conversion. The audio is then resampled to its original sampling rate.![alt text](https://github.com/kartikay-261/AI_VOICE_ASSISTANT/blob/main/download.png)
2. STT Conversion forgetting the prompt: We use whisper Ai a library from Open-Ai to transcribe the compiled and resampled audio in order to get the prompt given by the user. The code includes checks here and in each function as a fail-safe to check where the pipeline can burst.
3. LLM Response: This function receives the transcribed audio file from the previous function and passes it on to the LLM model (Microsoft/phi-2) to generate a text response to our query due to a lack of GPU this process is the bottle neck in my model. This could be performed much faster with the help of Cuda and using a distilled model. Google cloud services could also help in optimizing the process as we can choose a higher end machine to get our response.
4. Processing the audio: This function receives the audio and the control parameters (GENDER, PITCH, RATE, VOLUME) as its input and uses edge TTS to create an audio file of the same description. Currently this function randomly chooses between a male and female English speaker however this could be further optimised by using a language condition also.
Once the pipeline is done processing the audio file it is stored locally in a specified path as well as returned to the front end of the Gradio interface. The bottle neck in my pipeline is the LLM response being generated from my local resources, using a WebRTC channel on the client and server side and streaming the audio in real-time (not waiting for the entire audio to be finished) could help us reduce latency. The streaming app should have a built-in transcriber that only sends the text input to the server to minimize the latency. If this pipeline was orchestrated on a dedicated system with higher processing powers the pipeline could have been much faster
