The ORM Magic and The Service Layer
===================================

Author：201932110102，201932110101，201932110109，201932110108

Date：2021/12/25

Abstract
--------
·Understand dependency inversion.

·Map a class to a database table using SQLAlchemy’s ORM (object-relational mapper).

·Implement a service layer for a user to read an article.

·Practice Test-driven development (TDD).



Introduction
--------
We are going to understand how to keep the domain model pure by following the principle of dependency inversion - let the infrastructure depend on the domain model, but not the other way around.
Also, you are going to implement a service layer in services.py for EnglishPal, which provides a core service called read. This service would choose a suitable article for a user to read. The function read takes as input the following four arguments and returns an article ID if the user has been successfully assigned with an article to read.
·user: a User object. The class User is defined in model.py. User has an important method called read article.
·user repo: a UserRepository object. The class UserRepository is defined in repository.py.
·article repo: an ArticleRepository object. The class ArticleRepository is defined in repository.py.
·session: an SQLAlchemy session object.
The function read(user, user repo, article repo, session) raises an UnknownUser exception if user does not have a correct user name or a correct password, or raises a NoArticleMatched exception if no article in the article repository, i.e., article repo, has a difficulty level matching the user’s vocabulary level. We say that an article’s difficulty level, La, matches a user’s vocabulary level, Lu, iff La > Lu. If more than one article satisfies La > Lu, then the one with the smallest La is chosen.
An article’s difficulty level is recorded in the level field in the database table articles. A user’s vocabulary level is defined as the average value of top n most difficult words in the user’s list of new words (recorded in the database table newwords), where n is either 3 or the number of new words belonging to that user in the
table newwords, whichever is smaller.
For simplicity, we only consider the words in the following dictionary, where the values represent these words’difficulty levels.
d = {’starbucks’:5, ’luckin’:4, ’secondcup’:4, ’costa’:3, ’timhortons’:3,’frappuccino’:6}


Code
--------
1.orm.py： 
::

    # Software Architecture and Design Patterns -- Lab 3 starter code
    # Copyright (C) 2021 Hui Lan

    from sqlalchemy import Table, MetaData, Column, Integer, String, Date, ForeignKey
    from sqlalchemy.orm import mapper, relationship
    from sqlalchemy import create_engine

    import model

    metadata = MetaData()

    articles = Table(
        'articles',
        metadata,
        Column('article_id', Integer, primary_key=True, autoincrement=True),
        Column('text', String(10000)),
        Column('source', String(100)),
        Column('date', String(10)),
        Column('level', Integer, nullable=False),
        Column('question', String(1000)),
        )


    users = Table(
        'users',
        metadata,
        Column('username', String(100), primary_key=True),
        Column('password', String(64)),
        Column('start_date', String(10), nullable=False),
        Column('expiry_date', String(10), nullable=False),
        )

    newwords = Table(
        'newwords',
        metadata,
        Column('word_id', Integer, primary_key=True, autoincrement=True),
        Column('username', String(100), ForeignKey('users.username')),
        Column('word', String(20)),
        Column('date', String(10)),
        )

    readings = Table(
        'readings',
        metadata,
        Column('id', Integer, primary_key=True, autoincrement=True),
        Column('username', String(100), ForeignKey('users.username')),
        Column('article_id', Integer, ForeignKey('articles.article_id')),
        )


    def start_mappers():
        metadata.create_all(create_engine('sqlite:///EnglishPalDatabase.db'))#创建sqlite连接
        mapper(model.User, users)#初始化model参数，下同
        mapper(model.NewWord, newwords)
        mapper(model.Article, articles)
        mapper(model.Reading, readings)



2.services.py:

::

    # Software Architecture and Design Patterns -- Lab 3 starter code
    # An implementation of the Service Layer
    # Copyright (C) 2021 Hui Lan


    # word and its difficulty level
    WORD_DIFFICULTY_LEVEL = {'starbucks':5, 'luckin':4, 'secondcup':4, 'costa':3, 'timhortons':3, 'frappuccino':6}


    class UnknownUser(Exception):
        pass


    class NoArticleMatched(Exception):
        pass


    def read(user, user_repo, article_repo, session):
        u = user_repo.get(user.username)
        if u != None or u.password == user.password:#判断用户是否存在 密码是否正确

            articles = article_repo.list()#获取文章列表

            if articles == None:
                raise NoArticleMatched()#报错

            words = session.execute(
                'SELECT word FROM newwords WHERE username=:username',
                dict(username=user.username),
            )#从数据库获得用户的生词表

            sum = 0
            count = 0
            for word in words:
                sum += WORD_DIFFICULTY_LEVEL[word[0]]
                count += 1

            if count == 0:
                count = 1

            average = round(sum / count) + 1
             #生成生词表的平均难度等级
            while average<3:
                average+=1
            #如果平均难度等级<3就让它变成3
            for article in articles:
                if average != article.level:
                    continue
                article_id = user.read_article(article)
                session.add(model.Reading(username = user.username, article_id = article_id))
                session.commit()
                return article_id
            #对于每一篇文章判断其生词难度等级，文章难度等级与其生词难度等级相等时返回文章
            #session用于记录用户状态
            raise NoArticleMatched()
        else:
            raise UnknownUser()


Discussion
--------
Question:Does your function read in services.py follow the Single Respon sibility Principle (SRP) principle? 
Answer:Yes.Because each function is implemented using different function.In this way,we successfully reduces coupling between program contents,which means that the implementation of one function minimizes dependence on other functions.Therefore, when one function fails, the other functions will not be affected.



References
--------

Read the Docs. https://readthedocs.org/
