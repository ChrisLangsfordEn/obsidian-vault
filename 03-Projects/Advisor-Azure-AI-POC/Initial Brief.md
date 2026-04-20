
1. **Speech Recognition:** The frontend uses the Azure Speech SDK (`microsoft-cognitiveservices-speech-sdk`) in Conversation Transcriber mode with speaker diarization. Partial and final transcription results are streamed back and displayed live.    
2. **PII Detection:** When transcription completes for each utterance, the text is sent to the backend which calls Azure AI Language service to detect PII entities. Detected entities are highlighted in the UI with red, bold text and a wavy underline.    
3. **NBA Analysis:** Every 15 seconds during recording, the full transcript and client profile are sent to Azure OpenAI (GPT-4) for analysis. The AI generates 2-4 contextual suggestions that are displayed as cards in the UI.    
4. **Audio Recording:** The browser's `MediaRecorder` API simultaneously records audio. When stopped, the recording is uploaded to the backend.    
5. **Token Security:** Authentication uses `DefaultAzureCredential` (no keys in config). The backend obtains Entra ID access tokens and passes them to Azure services.  
6. **Storage:** The backend uploads audio (WebM) and transcript (TXT) files to Azure Blob Storage.


![[POC Sequence Diagram]]


#### Trevlen session on AOP & Telephony
- AOP -> Telephony control mechanism created by AOP developers (connects to separate telephony backend system)
- tap into voice backend
- Reactivity of AOP for streamed changes?
- 2 BE's - Communix (being decommissioned) & Cisco 
- Danie - looking at building a transcription set up for Cisco
- Cisco voice recording may not be in a working state --TBC with Danie
Cisco Finesse (browser based) - good place for these to live
- potentially a bigger value add
- does what AOP does (but better) --we have tried hand-rolling what finesse does in AOP

2 options - IX team (log project to make this work, and make custom controls etc for this)
Finesse - engage with Cisco?


- [ ] Follow up discussion with Danie
- [x] provide low level detail on what we are needing to "stream"? backend data vs web straight from microphone ✅ 2026-04-15

Friday/Monday response from Trevlen --can let Peter know progress is moving

New brief - build angular/react app (essentially copy PoC code base and make it function on our end)
- FNB Identity
- Kiro for the heavy lifting
- Integrate with our Azure instance / Sandbox instance?
- 