# deproject-spotify-top50-global-songs
Spotify ETL Data Pipeline using Python, AWS Lambda, S3, Glue, Cloudwatch and Athena

Data Pipeline for spotify top 50 Global 50 Hits steps involved:

1. First step is to extract data from api for spotify;

go to website;
https://developer.spotify.com/dashboard
>> login Spotify, I’ve logged in using crystal051997@gmail.com
Click create app,
 
 
 
We have clientid and secretid,
Clientid: 0a9150987b1f45f8a5f8cfcf34fb81f1
ClientSecretid: b1a20398bea54cd2a1473715986662a1
2. Now go back to your jupyter notebook;
!pip install spotipy
 
Read detailed documentation on it: https://spotipy.readthedocs.io/en/2.24.0/
, it has all the functions which we will use to extract the data

>>write the python scripts for that
https://open.spotify.com/playlist/1KNl4AYfgZtOVm9KHkhPTF
exctrating Json data from global top hitz using global top 50 link from spotify using this link then doing python operations and working on it.
>>Next,
3,
Go to cloudwatch set alarm for billing if u don’t want to get charged more, and keep a cap for it.
4,
Next,
Go to Amazon S3, we will create a. bucket
Write the name: spotify-etl-project-cp
keep everything same and create bucket
>>Under spotify etl project create subfolders; as raw_data & transformed_data

 
>>

Now, under Amazon S3, Amazon S3 >Buckets>spotify-etl-project-cp
Create 2 folders in spotify-etl-project-cp 
1st raw_data >> & 2nd transformed_data
  
>>
4. Now open Lambda in new new tab,
Click on Create function inside Lambda,
Then, name it as
spotify_api_data_extract
then select python latest version as runtime, click create fn, our fn is created
never put the client id we generated and the secret id in our code, not the best practice

so once u land on this page:
 
click on config, then env variables
open python code window side by side,
copy the env variables, 
 
Now save these crucial info in the back end, so that u don’t have to have it in your code, and i can directly access the var and do it when i need.

After adding the python code click deploy,
Then click test, click configure test event, name it as test save,

Ater testing this code, 

import json
import os
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
import boto3
from datetime import datetime

def lambda_handler(event, context):
    
    cilent_id = os.environ.get('client_id')
    client_secret = os.environ.get('client_secret')
    
    client_credentials_manager = SpotifyClientCredentials(client_id=cilent_id, client_secret=client_secret)
    sp = spotipy.Spotify(client_credentials_manager = client_credentials_manager)
    playlists = sp.user_playlists('spotify')
    
    playlist_link = "https://open.spotify.com/playlist/1KNl4AYfgZtOVm9KHkhPTF"
    playlist_URI = playlist_link.split("/")[-1]
    
    spotify_data = sp.playlist_tracks(playlist_URI)   
    
    print(spotify_data

we get these errors; {
  "errorMessage": "Unable to import module 'lambda_function': No module named 'spotipy'",
  "errorType": "Runtime.ImportModuleError",
  "requestId": "fef33939-c995-4a93-9585-8e1d95ad6660",
  "stackTrace": []
}

Okay so for this error
  "errorMessage": "Unable to import module 'lambda_function': No module named 'spotipy'",
Since it lambda function cannot identify spotipy, click on the lambda function again, in new tab create a layer.
 
Click on the layer option then create layer 
Add these details then click create. So this zip file has spotipy layer   download info, this will install spotipy layer to lambda, since lambda soes not support zip so we hve to give the pip file manually to download it to it. 
Now go back to the page where ur code is, click on add a layer option.
Click on the custom layer choose spotipy layer.

So now its fine run the test again, the Jason data we can see is available now. 
Importboto3, 
is a package created by aws to programmatically communicate with the aws services.
Now we will dump the entire json into s3 ,

This is our finsl code
import json
import os
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
import boto3
from datetime import datetime

def lambda_handler(event, context):
    
    cilent_id = os.environ.get('client_id')
    client_secret = os.environ.get('client_secret')
    
    client_credentials_manager = SpotifyClientCredentials(client_id=cilent_id, client_secret=client_secret)
    sp = spotipy.Spotify(client_credentials_manager = client_credentials_manager)
    playlists = sp.user_playlists('spotify')
    
    playlist_link = "https://open.spotify.com/playlist/1KNl4AYfgZtOVm9KHkhPTF"
    playlist_URI = playlist_link.split("/")[-1]
    
    spotify_data = sp.playlist_tracks(playlist_URI)   
    
    cilent = boto3.client('s3')
    
    filename = "spotify_raw_" + str(datetime.now()) + ".json"
    
    cilent.put_object(
        Bucket="spotify-etl-project-cp",
        Key="raw_data/to_processed/" + filename,
        Body=json.dumps(spotify_data)
        )

Click on deploy, then run test
Need to create Iam role when we need 2 services to talk to each other 
Attach policy
Then once all errors are gone we can,
  

Now to check ur s3, u have the data to be processed in s3.
Then we can move the data, from to prpcessed to processed. 

>>
Now create spotify_transformation_load_function
 
![image](https://github.com/user-attachments/assets/44c193f7-07f4-4df4-98de-e6de2cb46c60)

