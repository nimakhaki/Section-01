# Section-01

## Summary
###### Looking for a job is one of the most confusing and challenging processes anyone has to face. Many factors need to be considered to find the right job. You do not know which job is the right job for your major, you do not have any employment experience, you do not know where to looking for the right job and etc. Yes, you are overwhelmed with those worries in mind.  Especially, this issue has become more nerve-wracking due to the current pandemic. Therefore, I decided to define a project to address these concerns and to determine the most influential factors in job searching.  In the beginning, I  determined the most required skills for data-related jobs (including data scientist, data analyst and data engineer) jobs posted in the Philadelphia area on the Indeed website.  I scraped data from indeed using the python language and Selenium library.  After data cleaning, to extract keywords/skills from job descriptions, I performed NLP, using python and NLTK library. I used Tableau for an initial exploratory analysis.  Throughout the project, I intend to expand the analysis and the prediction to develop an application that could be applied throughout the United States. I aim to determine and forecast which skills are most important and in-demand in which states.   I will determine the trends and patterns between salaries, skills, geographical locations, and areas of industry e.g. healthcare, government, business, media and etc. I believe the outcome application would be very beneficial for everyone who is seeking for the job and can provide important and hidden information. 
# Code
## First we need data
###### Selenium used to scrape the Indeed website.
```python
from selenium import webdriver
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
```
###### Chrome browser used and we need to specify driver path
```python
driver = webdriver.Chrome(executable_path=r'C:/Users/nima/Anaconda3/Lib/site-packages/selenium/webdriver/chromedriver.exe')
```
###### Choose the website
```python
driver.get('https://indeed.com')
```
###### Select search bar on indeed and advanced search
```python
select_search = driver.find_element_by_xpath("//*[@id='whatWhereFormId']/div[3]/button")
select_search.click()

advanced_search = driver.find_element_by_xpath("//a[contains(text(),'Advanced Job Search')]")
advanced_search.click()
```
###### Search for Data Science job, limit the results, sort them and click on search!
```python
search_job = driver.find_element_by_xpath('//input[@id="as_and"]')
search_job.send_keys(['data scientist'])

display_limit = driver.find_element_by_xpath('//select[@id="limit"]//option[@value="20"]')
display_limit.click()

sort_option = driver.find_element_by_xpath('//select[@id="sort"]//option[@value="date"]')
sort_option.click()

search_button = driver.find_element_by_xpath('//*[@id="fj"]')
search_button.click()
```
###### Choose wait time for scaning
```python
driver.implicitly_wait(2) 
```
###### Choose the variables which are suppose to collect
```python
titles=[]
companies=[]
locations=[]
links =[]
reviews=[]
salaries = []
descriptions=[] 
```
###### Collect the data for first six variables
###### There are several pages and we need to click next page after data collection for each page
```python
for i in range(0,20):    
    jobs = driver.find_elements_by_xpath('//div[contains(@class,"clickcard")]')    
    for job in jobs:    
        try:
            review = job.find_element_by_xpath('.//span[@class="ratingsContent"]').text
        except:
            review = "None"
        reviews.append(review)
        try:
            salary = job.find_element_by_xpath('.//span[@class="salaryText"]').text
        except:
            salary = "None"   
        salaries.append(salary)
        try:
            location = job.find_element_by_xpath('.//span[contains(@class,"location")]').text
        except:
            location = "None"     
        locations.append(location)
        try:
            title  = job.find_element_by_xpath('.//h2[@class="title"]//a').text
        except:
            title = job.find_element_by_xpath('.//h2[@class="title"]//a').get_attribute(name="title")
        titles.append(title) links.append(job.find_element_by_xpath('.//h2[@class="title"]//a').get_attribute(name="href"))
        companies.append(job.find_element_by_xpath('.//span[@class="company"]').text)
    try:
        next_page = driver.find_element_by_xpath('//a[@aria-label={}]//span[@class="pn"]'.format(i+2))
        next_page.click()
    except:
        next_page = driver.find_element_by_xpath('//a[@aria-label="Next"]//span[@class="np"]')
        next_page.click()       
```
###### To collect data for job description we need to use job links
```python
descriptions=[]
for link in links:
    driver.get(link)
    jobdescriptions = driver.find_element_by_xpath('//div[@id="jobDescriptionText"]').text
    descriptions.append(jobdescriptions)
```
###### To collect data for job description we need to use job links
```python
df=pd.DataFrame()
df['Title']=titles
df['Company']=companies
df['Location']=locations
df['Link']=links
df['Review']=reviews
df['Salary']=salaries
df['Description']=descriptions
```
## Let's Look at the data
```python
df
```
![image info](./pictures/table01.png)
