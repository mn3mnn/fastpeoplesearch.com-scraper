# fastpeoplesearch.com-scraper
python script to search for people (given their names and addresses) on fastpeoplesearch.com using selenium webdriver and extract the results into an excel sheet.

- NOTES
  - **fastpeoplesearch.com works only in the US, so you will need a VPN if you're outside the US.**


- Dependencies
  - Google Chrome
  - chromedriver : download chromedriver from the official download page, https://sites.google.com/a/chromium.org/chromedriver/downloads


- Requirements
  - openpyxl
  - selenium
  - undetected_chromedriver
  - bs4


- Usage

  1- create ```data.xlsx``` containing the names and addresses (cities or states) of people to search for, or just use the template in my repo.  

  2- modify variables in ```automateFastPeopleSearch.com.py```, replace your chrome profile path with the existing one or keep it to create new profile

  3- make sure all requirements are installed

  4- replace ```chromedriver.exe``` with compatible version of your chrome browser

  5- run ```automateFastPeopleSearch.com.py```

  6- if you got "Access Denied", then you should use VPN. you can install any vpn extension and use it. 


- Code snippets

 ```
  
def main():
    driver = open_chrome_with_profile()  # Open Chrome with profile
    driver.get("https://www.fastpeoplesearch.com/")  # Navigate to FastPeopleSearch.com
    # if access denied, wait for user to enable vpn (only for the first time)
    if "Access Denied" in driver.page_source:
        print("Access Denied")
        time.sleep(60)  # Wait for the user to enable vpn extension
        driver.get("https://www.fastpeoplesearch.com/")  # Navigate to FastPeopleSearch.com
        if "Access Denied" in driver.page_source:
            return 1

    wb, ws = open_xlsx_file()  # Open the Excel file
    # for each row in the Excel file search for the person and write the phones to the Excel file
    for row in range(2, ws.max_row + 1):
        # try searching for this person
        try:
            first_name = ws[FIRST_NAME_COL + str(row)].value
            last_name = ws[LAST_NAME_COL + str(row)].value
            address = ws[ADDRESS_COL + str(row)].value

            if (first_name is None and last_name is None) or address is None:
                continue

            # search for this person
            first_name = first_name.replace(" ", "-")
            last_name = last_name.replace(" ", "-")
            address = address.replace(" ", "-")
            driver.get("https://www.fastpeoplesearch.com/name/" + first_name + "-" + last_name + "_" + address)

            # try to get all phones for this person as a list of strings
            phones = extract_phones_from_page(driver.page_source)
            if phones:
                # write phones to Excel file
                print("Found " + str(len(phones)) + " phones for " + first_name + " " + last_name)
                write_phones_to_xlsx_file(wb, ws, phones, row)
            else:
                print("No phones found for " + first_name + " " + last_name)

            # wait 1 second before searching for the next person
            time.sleep(1)

        except Exception as e:
            print(str(e))
            continue

    wb.close()
    driver.close()

 ```
