Use blueprints to architect a web application
===================================

Author：201932110102，201932110101，201932110109，201932110108

Date：2021/12/13

Code
--------
1.upload.py： 
::

     from flask import Blueprint

upload_bp = Blueprint('upload', __name__)

@upload_bp.route('/upload')
def upload():
    page = '''<form action="/"method="post"enctype="multipart/form-data">
            <input type="file"name="file"><input name="description"><input type="submit"value="Upload"></form>'''
    return page

2.The class/function level dependencies:

::

      classDiagram
      InsertQuery ..> Sqlite3Template
      RecordQuery ..> Sqlite3Template
      WordFreq ..> wordfreqCMD
      main ..> InsertQuery
      main ..> RecordQuery
      main ..> WordFreq
      main ..> difficulty
      main ..> pickle_idea
      main ..> pickle_idea2
      main ..> wordfreqCMD
      wordfreqCMD ..> pickle_idea
      class main{
      +total_number_of_essays()
      +load_freq_history()
      +verify_user()
      +add_user()
      +check_username_availability()
      +get_expiry_date()
      +get_today_article()
      +get_flashed_messages()
      +mark_word()
      +mainpage()
      +user_mark_word()
      +unfamiliar()
      +familiar()
      +deleteword()
      +userpage()
      +signup()
      +login()
      }
      class difficulty{
       +load_record()
       +difficulty.get_difficulty_level()
       +difficulty.user_difficulty_level()
       +difficulty.text_difficulty_level()
      }
      class pickle_idea{
       +merge_frequency()
       +load_record()
       +save_frequency_to_pickle()
       +familiar()
      }
      class pickle_idea2{
       +merge_frequency()
       +save_frequency_to_pickle()
      }
      class Sqlite3Template{
       +do()
      }
      class WordFreq{
       +get_freq()
      }
   
.. image:: class2.png


3.Pros and cons of the current architecture of EnglishPal

Disadvantages: 

1)The speed of transferring picture or other media information between web pages is low. 

2)The server processes multiple requests at the same time, which reduces the operation efficiency. 

3)Code change and maintenance are difficult. 

      
Advantages: 

1)API has high security. 

2)Using syntax similar to the pattern for development makes the code readable. 

3)Simple crud and small code base are suitable for smaller projects. 

4)There is less communication between the front end and the back end, reducing the communication cost. 
      

Discussions
--------
During the lab, we learnt to use Snakefood, Graphviz Online, Mermaid as well as Read the Docs. We figured the current health status of the architecture of EnglishPal which can be conducive to the projects we may develop or improve in the future.

References
--------
Graphviz. https://graphviz.org/

Graphviz Online. https://bit.ly/3uYDiLV

Snakefood: Python Dependency Graphs. http://furius.ca/snakefood/

Mermaid. https://mermaid-js.github.io/mermaid/#/

Read the Docs. https://readthedocs.org/

Sofia Peterson. A Brief Guide How to Write a Computer Science Lab Report. https://thehackpost.com/a-brief-guide-how-to-write-a-computer-science-lab-report.html




