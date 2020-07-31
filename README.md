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
![table01](https://user-images.githubusercontent.com/69024956/89049988-17353a80-d320-11ea-9126-63b4892944b7.PNG)

### Now lets see to one of job description
###### "Description\nThe Senior SQL Developer on the Data & Process Modernization team is responsible for delivering one or more of the following services:\nData Strategy & Architecture Partnerships - Collaborate with teams in the Technology Services Organization and Enterprise Data Organization on development and implementation of data asset roadmap, data assets, and sourcing new data for the purposes of Incentives & Sales Performance Management\nTactical remediation of existing/aging data assets – Data feeds and prep (ETL), process review\nStrategic Project Advisory – Engage in strategic initiatives to ensure that the information needs for the purposes of Incentives & Sales Performance are discovered and realized.\nQualifications\nRequired Skills / Experience\nAdvanced knowledge of SQL programming language\nAdvanced knowledge in ETL processes, create and/or modify data marts\nStrong understanding of big data and data lakes (e.g., Data Modeling)\nCompetent use of Microsoft Office applications to include Microsoft Project, Excel, Visio, PowerPoint, Word\nSelf-thinker able to work through issues and escalate only when appropriate\nStrong oral communication skills\nAbility to act as internal consultant, building relationships with partners, active engagement\nAbility to manage the deliverable lifecycle: elicit requirements, update/create documentation, provide estimates, drive to completion\nAbility to derive insights from complex data sets including basic statistical techniques, control plans, correlations\nService Level Management - ability to manage competing priorities while meeting negotiated SLAs\n5+ years working in providing technical delivery in support of Data needs\nPreferred Skills / Experience\nAgile Kanban\nKnowledge of migrating data from Excel VBA and Access data stores to SQL\nServer Administrator experience, such partitioning, back-ups\n5+ years experience in customer facing front-line role and/or roles supporting front-line\nAtleryx experience or certification desired\nFinancial Services background desired\nHours and Work Schedule\nHours per week: 40\nWork Schedule: 8:00am - 5:00pm, Monday - Friday\nWhy Work for Us\nAt Citizens, you'll find a customer-centric culture built around helping our customers and giving back to our local communities. When you join our team, you are part of a supportive and collaborative workforce, with access to training and tools to accelerate your potential and maximize your career growth.\nEqual Employment Opportunity\nIt is the policy of Citizens Bank to provide equal employment and advancement opportunities to all colleagues and applicants for employment without regard to race, color, ethnicity, religion, gender, pregnancy/childbirth, age, national origin, sexual orientation, gender identity or expression, disability or perceived disability, genetic information, citizenship, veteran or military status, marital or domestic partner status, or any other category protected by federal, state and/or local laws.\nEqual Employment and Opportunity Employer/Disabled/Veteran\nCitizens Bank is a brand name of Citizens Bank, N.A. and each of its respective subsidiaries."

###### To extract the Skills and techniques we neet to do NLP on the job descriptions
## NLP
###### NLTK library is used for this project
```python
import numpy as np
import pandas as pd
import re
import nltk
from nltk.corpus import stopwords
from nltk.stem.porter import PorterStemmer
```
###### Define the text processing
```python
jd = df_da.drop(['Link', 'Salary','Review','Location','Company','Title'], axis=1)
def text_preprocessing(x):
    words = re.sub('[^a-zA-Z]', ' ', x).lower().split()
    lemma = nltk.wordnet.WordNetLemmatizer()
    words = [lemma.lemmatize(word) for word in words]
    words = ' '.join(words)
    return words
jd = jd.applymap(text_preprocessing)
```
###### Create the matrix X for keywords
```python
X = pd.DataFrame()
for column in jobs_X:
    vect = TfidfVectorizer(stop_words='english', min_df=0.005, ngram_range=(1,2))
    X_sub = vect.fit_transform(jd[column])
    new_sub = pd.DataFrame(X_sub.toarray(), columns=vect.get_feature_names())
    X = pd.concat([X, new_sub], axis=1, sort=False)
```
###### Create a bag of Data Science skills
```python
skills = ['Scala', 'visualisation', 'Perl', 'Java', 'Matlab', 'JavaScript', 'math', 'mathematics',
          'Python', 'SPSS', 'saas', 'Tableau', 'Excel', 'Azure', 'cloud', 'API', 'qlik','qlikview',
          'Hadoop', 'Pig', 'Spark', 'statistic', 'MapReduce', 'Hive', 'modeling', 
          'Machine Learning', 'ML', 'AI', 'Flume', 'HBase', 'Cassandra', 'NoSQL', 'SQL', 'MongoDB',           'Power BI', 'PowerBi', 'NLP', 'AWS']
skills = [s.lower() for s in skills]
```
###### Check the match for each row and let see some example
```python
for i in range(90):
    for j in skills_lower:
        if X[j][i] != 0:
            print(str(i) + ", " + str(j))
```
######
0, excel
0, modeling
0, sql
1, python
1, saas
1, cloud
1, nosql
1, sql
1, aws
2, python
2, cloud
2, sql
3, scala
3, java
3, python
3, spss
3, tableau
3, cloud
3, qlikview
3, hadoop
3, spark
3, hive
3, modeling
3, flume
3, hbase
3, nosql
3, sql
3, power bi
3, aws
4, python
4, cloud
4, sql
4, aws
5, scala
5, mathematics
5, python
5, spark
5, statistic
5, hive
5, sql
6, python
6, machine learning
6, ml
6, nlp
6, aws
7, python
7, modeling
9, python
9, api
9, modeling
9, machine learning
9, ml
9, ai
9, aws
## Data is ready!
###### Now we will combine the first table with 7 variables and second one with skills through the field ID
###### Now to do some explotory analysis, tableau has been used.
###### First plot: Top Skills needed in data related field (Data Science/Analytics/Engineer)
![table02](https://user-images.githubusercontent.com/69024956/89053346-10f58d00-d325-11ea-8d52-d271cdfd3d39.PNG)
###### Second Plot: Top Skills in Data Science
![table03](https://user-images.githubusercontent.com/69024956/89053464-397d8700-d325-11ea-863e-6c1ecfe60676.PNG)
###### Third Plot: Top Skills in Data Engineer
![table04](https://user-images.githubusercontent.com/69024956/89053525-5619bf00-d325-11ea-9586-f8d9fd49bb0d.PNG)
###### Forth Plot: Top Skills in Data Analytics
![table05](https://user-images.githubusercontent.com/69024956/89053567-6af65280-d325-11ea-8a23-a22fee09dfb4.PNG)





















