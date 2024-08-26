# web-scrapping
web scrapping with beautifulsoup.py
import requests
from bs4 import BeautifulSoup
import csv

# URLs for each location
urls = [
    "https://targetstudy.com/colleges/engineering-colleges-in-telangana.html",
    "https://targetstudy.com/colleges/engineering-colleges-in-patna.html",
    "https://targetstudy.com/colleges/engineering-colleges-in-varanasi.html",
    "https://targetstudy.com/colleges/engineering-colleges-in-noida.html",
    "https://targetstudy.com/colleges/engineering-colleges-in-lucknow.html"
]

# Function to extract phone numbers and emails
def extract_contact_info(soup):
    phone_numbers = []
    email_addresses = []

    # Find all list items with class "list-group-item d-flex flex-row align-items-center"
    items = soup.find_all('li', class_='list-group-item d-flex flex-row align-items-center')

    for item in items:
        # Check if the item contains a phone number
        if item.find('i', class_='material-icons pmd-list-icon pmd-icon-md md-dark', text='call'):
            phone = item.find('h5', class_='pmd-list-title').get_text(strip=True)
            phone_numbers.append(phone)

        # Check if the item contains an email address
        if item.find('i', class_='material-icons pmd-list-icon pmd-icon-md md-dark', text='email'):
            email_img = item.find('img')
            if email_img:
                email = "Email address hidden behind CAPTCHA image"
                email_addresses.append(email)

    return phone_numbers, email_addresses

# CSV file to store the data
csv_file = "institute_contacts.csv"

# Open the CSV file in write mode
with open(csv_file, mode='w', newline='', encoding='utf-8') as file:
    writer = csv.writer(file)
    # Write the header
    writer.writerow(["Location", "Phone Numbers", "Email Addresses"])

    # Iterate over the URLs
    for url in urls:
        print(f"Scraping data from: {url}")
        
        # Send HTTP request
        response = requests.get(url)
        
        # Parse HTML content
        soup = BeautifulSoup(response.text, 'html.parser')
        
        # Extract contact information
        phones, emails = extract_contact_info(soup)
        
        # Write the data into the CSV file
        for phone, email in zip(phones, emails):
            writer.writerow([url, phone, email])
        
        print(f"Data from {url} has been written to {csv_file}.\n")

print(f"Scraping completed. Data saved in {csv_file}.")
