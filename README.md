<<<<<<< HEAD
# google_maps_api
=======
# Google Maps API Lead Scraper - Mindset Coaches

## Overview
This project scrapes **Mindset Coaches** and related professionals from **Google Maps** using the **Google Places API**. The script retrieves:

✅ **Business Name**  
✅ **Address**  
✅ **Phone Number**  
✅ **Website**  
✅ **Social Media Links** (if available via website scraping)  

The extracted data is saved into a **CSV file** for easy access.

---

## Features
✔ **Google Maps Data Extraction**: Retrieves business details from Google Places API.  
✔ **Website Scraping**: Extracts social media links from business websites.  
✔ **CSV Export**: Saves structured data for easy analysis.    

---

## Installation

### 1. Get a Google API Key
1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Enable the following APIs:
   - **Places API**
   - **Maps API**
3. Generate an API key and enable billing.

### 2. Install Dependencies
Run the following command to install required Python libraries:
```bash
pip install requests beautifulsoup4 pandas
```

## Usage

### 1.Update API Key
use the google maps api key:
```python
API_KEY = 'Ap1K3yWaShERe15weaR'
```

### 2.Run this Script
Execute the script with:
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

API_KEY = 'Ap1K3yWaShERe15weaR'
SEARCH_URL = "https://maps.googleapis.com/maps/api/place/textsearch/json"
DETAILS_URL = "https://maps.googleapis.com/maps/api/place/details/json"
LOCATION = '51.5074,-0.1278'  # London
RADIUS = 50000  # Radius in meters
KEYWORDS = ['mindset coach', 'life coach', 'personal development coach']

def get_place_details(place_id):
    """Fetch detailed information about a place."""
    params = {
        'place_id': place_id,
        'fields': 'name,formatted_address,formatted_phone_number,website',
        'key': API_KEY
    }
    response = requests.get(DETAILS_URL, params=params)
    if response.status_code == 200:
        return response.json().get('result', {})
    else:
        print(f"Error fetching details for place_id {place_id}: {response.status_code}")
        return {}

def extract_social_links(website_url):
    """Scrape a website to extract social media links."""
    social_links = []
    try:
        response = requests.get(website_url, timeout=10)
        if response.status_code == 200:
            soup = BeautifulSoup(response.text, 'html.parser')
            links = soup.find_all('a', href=True)
            social_links = [link['href'] for link in links if any(platform in link['href'] for platform in 
                          ['facebook.com', 'instagram.com', 'linkedin.com', 'twitter.com'])]
    except Exception as e:
        print(f"Error scraping {website_url}: {e}")
    return social_links

def search_places():
    """Search places using Google Maps API and get detailed info."""
    all_places = []
    for keyword in KEYWORDS:
        params = {
            'query': keyword,
            'location': LOCATION,
            'radius': RADIUS,
            'key': API_KEY
        }
        response = requests.get(SEARCH_URL, params=params)
        if response.status_code == 200:
            results = response.json().get('results', [])
            for place in results:
                place_id = place.get('place_id')
                if place_id:
                    details = get_place_details(place_id)
                    website = details.get('website')
                    social_links = extract_social_links(website) if website else []
                    all_places.append({
                        'Name': details.get('name'),
                        'Address': details.get('formatted_address'),
                        'Phone': details.get('formatted_phone_number'),
                        'Website': website,
                        'Social Media Links': ', '.join(social_links)
                    })
        else:
            print(f"Error with search for keyword '{keyword}': {response.status_code}")
    return all_places

# Step 1: Fetch all places and their details
places_data = search_places()

# Step 2: Save to CSV
df = pd.DataFrame(places_data)
output_file = 'mindset_coaches.csv'
df.to_csv(output_file, index=False)
print(f"Data saved to {output_file}")

# Step 3: Display Results
for place in places_data:
    print(f"Name: {place['Name']}")
    print(f"Address: {place['Address']}")
    print(f"Phone: {place['Phone']}")
    print(f"Website: {place['Website']}")
    print(f"Social Media Links: {place['Social Media Links']}")
    print("-" * 50)
```

### 3.View the Results
After running, the results will be saved as:
[mindset_coaches.csv]

### Thank you for checking out my project.

>>>>>>> 8865e0c (first commit)
