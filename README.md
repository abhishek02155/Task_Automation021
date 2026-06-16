# Task_Automation021
By this we can easly copy in folder to anyother new folder 
"""
CodeAlpha - Python Programming Internship
Task 3: Task Automation with Python Scripts
This script automates small, real-life repetitive tasks.
It includes all three suggested ideas, selectable via a menu:
  1. Move all .jpg files from a folder to a new folder.
  2. Extract all email addresses from a .txt file and save them to another file.
  3. Scrape the title of a fixed webpage and save it.
Key Concepts Used: os, shutil, re, requests, file handling.
"""

import os
import shutil
import re

# requests is only needed for option 3 (web scraping).
# It is imported lazily inside that function so the rest of the script still works even if 'requests' isn't installed.

def move_jpg_files():
    """Move all .jpg files from a source folder to a destination folder."""
    source_folder = input("Enter the source folder path: ").strip()
    dest_folder = input("Enter the destination folder path: ").strip()

    if not os.path.isdir(source_folder):
        print(f"Error: '{source_folder}' is not a valid directory.")
        return

    # Create destination folder if it doesn't exist
    os.makedirs(dest_folder, exist_ok=True)

    moved_count = 0
    for filename in os.listdir(source_folder):
        if filename.lower().endswith((".jpg", ".jpeg")):
            src_path = os.path.join(source_folder, filename)
            dest_path = os.path.join(dest_folder, filename)
            shutil.move(src_path, dest_path)
            print(f"Moved: {filename}")
            moved_count += 1

    if moved_count == 0:
        print("No .jpg files found in the source folder.")
    else:
        print(f"\nDone! Moved {moved_count} .jpg file(s) to '{dest_folder}'.")

def extract_emails():
    """Extract all email addresses from a .txt file and save them to another file."""
    input_file = input("Enter the path to the input .txt file: ").strip()
    output_file = input("Enter the path for the output file (e.g., emails.txt): ").strip()

    if not os.path.isfile(input_file):
        print(f"Error: '{input_file}' does not exist.")
        return

    # Regular expression pattern for matching email addresses
    email_pattern = r"[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}"

    with open(input_file, "r", errors="ignore") as f:
        content = f.read()

    emails_found = re.findall(email_pattern, content)

    # Remove duplicates while preserving order
    unique_emails = list(dict.fromkeys(emails_found))

    if not unique_emails:
        print("No email addresses found in the file.")
        return

    with open(output_file, "w") as f:
        for email in unique_emails:
            f.write(email + "\n")

    print(f"\nFound {len(unique_emails)} unique email address(es).")
    print(f"Saved to '{output_file}'.")

def scrape_webpage_title():
    """Scrape the title of a fixed webpage and save it to a text file."""
    try:
        import requests
        from html.parser import HTMLParser
    except ImportError:
        print("The 'requests' library is required for this option.")
        print("Install it using: pip install requests")
        return

    # A "fixed" webpage URL - can be changed as needed
    url = input("Enter the webpage URL (press Enter to use https://example.com): ").strip()
    if not url:
        url = "https://example.com"

    class TitleParser(HTMLParser):
        def __init__(self):
            super().__init__()
            self.in_title = False
            self.title = ""

        def handle_starttag(self, tag, attrs):
            if tag.lower() == "title":
                self.in_title = True

        def handle_endtag(self, tag):
            if tag.lower() == "title":
                self.in_title = False

        def handle_data(self, data):
            if self.in_title:
                self.title += data

    try:
        response = requests.get(url, timeout=10)
        response.raise_for_status()
    except requests.RequestException as e:
        print(f"Error fetching the webpage: {e}")
        return

    parser = TitleParser()
    parser.feed(response.text)
    title = parser.title.strip()

    if not title:
        print("No <title> tag found on the page.")
        return

    output_file = "webpage_title.txt"
    with open(output_file, "w") as f:
        f.write(f"URL: {url}\n")
        f.write(f"Title: {title}\n")

    print(f"\nPage title: {title}")
    print(f"Saved to '{output_file}'.")

def main():
    while True:
        print("\n" + "=" * 45)
        print("TASK AUTOMATION MENU")
        print("=" * 45)
        print("1. Move all .jpg files to a new folder")
        print("2. Extract emails from a .txt file")
        print("3. Scrape the title of a webpage")
        print("4. Exit")

        choice = input("Choose an option (1-4): ").strip()

        if choice == "1":
            move_jpg_files()
        elif choice == "2":
            extract_emails()
        elif choice == "3":
            scrape_webpage_title()
        elif choice == "4":
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Please enter 1, 2, 3, or 4.")

if __name__ == "__main__":
    main()

