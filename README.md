- 👋 Hi, I’m @likitha31
- 👀 I’m interested in software development and drbfm
- 🌱 I’m currently learning node js 
- 💞️ I’m looking to collaborate on any intresting projects
- 📫 How to reach me through my mail: lkg310520@gmail.com
import csv
import json
import os
import requests

from collections import defaultdict


# Function to convert a CSV to JSON
# Takes the file paths as arguments
def make_json(csvFilePath, jsonFilePath):
    
    data_dict = defaultdict(dict)
    #Open and Read the IFSC.CSV File using csv reader
    with open(csv_file_path, encoding = 'utf-8') as csv_file_handler:
        
        csvReader = csv.DictReader(csv_file_handler)
        # Store the required columns in out
        out =[{"MICR": row['MICR'],"STATE": row['STATE'],"IFSC": row['IFSC'],"BRANCH": row['BRANCH'],"CITY": row['CITY']} for row in csvReader]
    
        #Make the First four characters of IFSC code as KEY   
        for row in out:
            key = row['IFSC'][:4]
            data_dict[key][row['IFSC']] = row
    #Create and save json files in folder
    for key, values in data_dict.items():
        with open(f'{json_base_path}_{key}.json', 'w', encoding='utf-8') as json_file_handler:
            json_file_handler.write(json.dumps(values, indent = 4))
		
#Function to download the latest IFSC File from github
#Takes URL and Folder path to save file as argument
def download(url: str, dest_folder: str):
    if not os.path.exists(dest_folder):
        # create folder if it does not exist
        
        os.makedirs(dest_folder)  
        
        #file name
    filename = url.split('/')[-1].replace(" ", "IFSC")  
    file_path = os.path.join(dest_folder, filename)

    r = requests.get(url, stream=True)
    if r.ok:
        print("saving to", os.path.abspath(file_path))
        with open(file_path, 'wb') as f:
            for chunk in r.iter_content(chunk_size=1024 * 8):
                if chunk:
                    f.write(chunk)
                    f.flush()
                    os.fsync(f.fileno())
    else: 
        # HTTP status code 4XX/5XX
        print("Download failed: status code {}\n{}".format(r.status_code, r.text))


download("https://github.com/razorpay/ifsc/releases/latest/download/IFSC.csv", dest_folder="mydir")

csv_file_path = r'C:\Users\Likhitha\Desktop\internSam\mydir\IFSC.csv'
json_base_path = r'IFSCJSON.json'

# Call the make_json function
make_json(csv_file_path, json_base_path)

#import urllib.request 
#rllib.request.urlretrieve(url, filename)

