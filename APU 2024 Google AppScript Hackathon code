const geminiAiApiKey = 'AIzaSyBD0WY8v75ghXTk6CaDfIg44rmLEdE4WE4';
const googleCloudProjectId = 'dogwood-keep-430614-f1';
const docsID = '1Z697fMTuKzOtvwJx3NFFHP4sLq7MrifjMZeurQS0Tm4';

// Function to transcribe audio using Google Cloud Speech-to-Text
function transcribeAudio(fileId) {
  try {
    Logger.log('Attempting to get file by ID: ' + fileId);
    const file = DriveApp.getFileById(fileId);
    Logger.log('File obtained successfully: ' + file.getName());
    
    const blob = file.getBlob();
    const audio = Utilities.base64Encode(blob.getBytes());
    
    const requestPayload = {
      "config": {
        "encoding": "MP3",
        "sampleRateHertz": 16000,
        "languageCode": "en-US"
      },
      "audio": {
        "content": audio
      }
    };

    const options = {
      "method": "post",
      "contentType": "application/json",
      "payload": JSON.stringify(requestPayload),
    };

    const url = 'https://speech.googleapis.com/v1/speech:recognize?key=AIzaSyBLi6DmuYYTMt9nvhdlV3DLiQg31cISeO8';
    const response = UrlFetchApp.fetch(url, options);
    const responseData = JSON.parse(response.getContentText());
    Logger.log('Speech-to-Text API response: ' + JSON.stringify(responseData));

    if (responseData.results && responseData.results.length > 0) {
      return responseData.results.map(result => result.alternatives[0].transcript).join('\n');
    } else {
      Logger.log('No transcription results found.');
      return null;
    }
  } catch (error) {
    Logger.log('Error during transcription: ' + error.message);
    return null;
  }
}

function generateContentWithGemini(text) {
  const url = 'https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent?key=AIzaSyBD0WY8v75ghXTk6CaDfIg44rmLEdE4WE4';

  const payload = {
    "contents": [
      {
        "role": "user",
        "parts": [
          {
            "text": 'Analyze the following customer call transcript:\n\n' + text
          }
        ]
      }
    ]
  };

  const options = {
    "method": "post",
    "contentType": "application/json",
    "payload": JSON.stringify(payload)
  };

  
  const response = UrlFetchApp.fetch(url, options);
  const json = JSON.parse(response);
  return json.candidates[0].content.parts[0].text;
}

function appendTextToGoogleDoc(docId, textToAppend) {
  // Open the Google Doc by its ID
  var doc = DocumentApp.openById(docId);

  // Get the body of the document
  var body = doc.getBody();

  // Append the text to the document body
  body.appendParagraph(textToAppend);

  Logger.log('Text appended successfully.');
}

// Main function to transcribe and analyze the call
function myFunction() {
  const fileId = '1Xn6jp9QJoeStR0dQcU5bxa9QbnFxkuIl';
  Logger.log('File ID: ' + fileId);

  var transcript = transcribeAudio(fileId);
  if (transcript) {
    const analysis = "\n" + generateContentWithGemini(transcript); 
    appendTextToGoogleDoc(docsID, analysis);
  } else {
    Logger.log('Transcription failed.');
  }
}

myFunction();

