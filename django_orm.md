# Django ORM
## Examples and Practice Problems

This article will provide you problems and solutions based on dataset to help you practice Django ORM and take you from beginner to professional in making queries using Django ORM. You can see Django Documentation for getting familiar with how to make models and queries using Django, then use this article as means to practice and notch up your skills.

The dataset for these exercises is for a library-like website where records of Books, Authors and Publishers are stored. Librarian wants to collect key information about this data, and you will be helping him by writing queries for the same.

For getting started, copy the following files in your main app in your Django project. populate the database with a dump sql file. And GO!
```
Import: django_orm_dump.sql
```


```python
from django.db import models

class Author(models.Model):
  firstname = models.CharField(max_length=100)
  lastname = models.CharField(max_length=100)
  address = models.CharField(max_length=200, null=True)
  zipcode = models.IntegerField(null=True)
  telephone = models.CharField(max_length=100, null=True)
  recommendedby = models.ForeignKey('Author',
    on_delete=models.CASCADE,
    related_name='recommended_authors',
    related_query_name='recommended_authors',
    null=True)
  joindate = models.DateField()
  popularity_score = models.IntegerField()
  followers = models.ManyToManyField('User',
    related_name='followed_authors',
    related_query_name='followed_authors')
  def __str__(self):
    return self.firstname + ' ' + self.lastname

class Books(models.Model):
  title = models.CharField(max_length=100)
  genre = models.CharField(max_length=200)
  price = models.IntegerField(null=True)
  published_date = models.DateField()
  author = models.ForeignKey('Author',
    on_delete=models.CASCADE,
    related_name='books',
    related_query_name='books')
  publisher = models.ForeignKey('Publisher',
    on_delete=models.CASCADE,
    related_name='books',
    related_query_name='books')
  def __str__(self):
    return self.title

class Publisher(models.Model):
  firstname = models.CharField(max_length=100)
  lastname = models.CharField(max_length=100)
  recommendedby = models.ForeignKey('Publisher',
    on_delete=models.CASCADE,
    null=True)
   joindate = models.DateField()
  popularity_score = models.IntegerField()
  def __str__(self):
    return self.firstname + ' ' + self.lastname

class User(models.Model):
  username = models.CharField(max_length=100)
  email = models.CharField(max_length=100)
  def __str__(self):
    return self.username
```

Exercises:
1. Write a Query using Django ORM to fetch all the books objects from your database.
2. Write a Query using Django ORM to fetch title and published_date of all books from the database.
3. Fetch first name and last name of all the new authors ( Authors with popularity_score = 0 are new authors ).
4. Fetch first name and popularity score of all authors whose first name starts with A and popularity score is greater than or equal to 8.
5. Fetch first name of all the authors with aa case insensitive in their first name.
6. Fetch list of all the authors whose ids are in the list = [1, 3, 23, 43, 134, 25].
7. Fetch list of all the publishers who joined after or in September 2012, output list should only contain first name and join date of publisher. Order by join date.
8. Fetch ordered list of first 10 last names of Publishers, list must not contain duplicates.
9. Get the signup date for the last joined Author and Publisher.
10. Get the first name, last name and join date of the last author who joined.
11. Fetch list of all authors who joined after or in year 2013
12. Fetch total price of all the books written by authors with popularity score 7 or higher.
13. Fetch list of titles of all books written by authors whose first name starts with ‘A’. The result should contain a list of the titles of every book. Not a list of tuples.
14. Get total price of all the books written by author with pk in list [1, 3, 4]
15. Produce a list of all the authors along with their recommender.
16. Produce list of all authors who published their book by publisher pk = 1, output list should be ordered by first name.
17. Create three new users and add in the followers of the author with pk = 1.
18. Set the followers list of the author with pk = 2, with only one user.
19. Add new users in followers of the author with pk = 1.
20. Remove one user from the followers of the author with pk = 1.
21. Get first names of all the authors, whose user with pk = 1 is following. ( Without Accessing Author.objects manager )
22. Fetch list of all authors who wrote a book with “tle” as part of Book Title.
23. Fetch the list of authors whose names start with ‘A’ case insensitive, and either their popularity score is greater than 5 or they have joined after 2014. with Q objects.
24. Retrieve a specific object with primary key= 1 from the Author table.
25. Retrieve the first N=10 records from an Author table.
26. Retrieve records from a table that match this condition, popularity score = 7. And get the first and last record of that list.
27. Retrieve all authors who joined after or in the year 2012, popularity score greater than or equal to 4, join date after or with date 12, and first name starts with ‘a’ (case insensitive) without using Q objects.
28. Retrieve all authors who did not join in 2012.
29. Retrieve Oldest author, Newest author, Average popularity score of authors, sum of price of all books in database.
30. Retrieve all authors who have no recommender, recommended by field is null.
31. Retrieve the books that do not have any authors, where the author is null. Also, retrieve the books whose authors are present, but do not have a recommender, where the author is not null and the author’s recommender is null. (Note that if the condition for the author not being null is not specified and only the condition for the recommender being null is mentioned, all books with both author null and author’s recommender null will be retrieved.)
32. Total price of books written by author with primary key = 1. ( Aggregation over related model ), oldest book written by author with pk = 1, latest book written by author with pk = 1.
33. Among the publishers in the Publishers table what is the oldest book any publisher has published.
34. Average price of all the books in the database.
35. Maximum popularity score of publisher among all the publishers who published a book for the author with pk = 1. (Reverse Foreign Key hop)
36. Count the number of authors who have written a book which contains the phrase ‘ab’ case insensitive.
37. Get all the authors with followers more than 216.
38. Get average popularity score of all the authors who joined after 20 September 2014.
39. Generate a list of books whose author has written more than 10 books.
40. Get the list of books with duplicate titles.

Note: Combining multiple aggregations with annotation will yield the wrong results because joins are used instead of subqueries. For most aggregates there is no way to avoid this problem. Count aggregate has distinct parameter that may help. Consider inspecting the query property of the QuerySet object. ( Taken from docs of django as key information )

Solutions:
```python
from main.models import *
import datetime
from django.db.models import Count, Avg, Sum, Max, Min
from django.db.models import Q, F

ans1 = Books.objects.all()

ans2 = Books.objects.all().values_list('title', 'published_date')

ans3 = Authors.objects.all().filter(popularity_score=0).values_list('firstname', 'lastname')

ans4 = Authors.objects.all().filter(firstname__startswith='a', popularity_score__gte=8).values_list('firstname', 'popularity_score')

ans5 = Authors.objects.all().filter(firstname__icontains='aa').values_list('firstname')

ans6 = Authors.objects.all().filter(pk__in=[1, 3, 23, 43, 134, 25])

ans7 = Authors.objects.all().filter(joindate__gte=datetime.date(year=2012, month=9, day=1)).order_by('joindate').values_list('firstname', 'joindate')

ans8 = Publishers.objects.all().order_by('lastname').values_list('lastname').distinct()[:10]

ans9 = [Authors.objects.all().order_by('joindate').last(), Publishers.objects.all().order_by('-joindate').first()]

ans10 = Authors.objects.all().order_by('-joindate').values_list('firstname', 'lastname', 'joindate').first()

ans11 = Authors.objects.all().filter(joindate__year__gte=2013)

ans12 = Books.objects.all().filter(author__popularity_score__gte=7).aggregate(total_book_price=Sum('price'))

ans13 = Books.objects.all().filter(author__firstname__contains='a').values_list('title', flat=True)

ans14 = Books.objects.all().filter(author__pk__in=[1, 3, 4]).aggregate('price')

ans15 = Authors.objects.all().values_list('firstname', 'recommendedby__firstname')

ans16 = Authors.objects.all().filter(books__publisher__pk=1)

user1 = Users.objects.create(username='user1', email='user1@test.com')
user2 = Users.objects.create(username='user2', email='user2@test.com')
user3 = Users.objects.create(username='user3', email='user3@test.com')
ans17 = Authors.objects.get(pk=1).followers.add(user1, user2, user3)

ans18 = Authors.objects.get(pk=2).followers.set(user1)

ans19 = Authors.objects.get(pk=1).followers.add(user1)

ans20 = Authors.objects.get(pk=1).followers.remove(user1)

ans21 = Users.objects.get(pk=1).followed_authors.all().values_list('firstname', flat=True)

ans22 = Authors.objects.all().filter(books__title__icontains='tle')

ans23 = Authors.objects.all().filter(Q(firstname__istartswith='a') and ( Q(popularity_score__gt=5) or Q(joindate__year__gt=2014)))

ans24 = Authors.objects.all().get(pk=1)

ans25 = Authors.objects.all()[:10]

qs = Authors.objects.all().filter(popularity_scre=7)
author1 = qs.first()
author2 = qs.last()
ans26 = [author1, author2]

ans27 = Authors.objects.all().filter(joindate__year__gte=2012, popularity_score__gte=4, joindate__day__gte=12, firstame__istartswith='a')

ans28 = Authors.objects.all().exclude(joindate__year=2012)

oldest_author = Authors.objects.all().aggregate(Min('joindate'))
newest_author = Authors.objects.all().aggregate(Max('joindate'))
avg_pop_score = Authors.objects.all().aggregate(Avg('popularity_score'))
sum_price = Books.objects.all().aggregate(Sum('price'))
ans29 = [oldest_author, newest_author, avg_pop_score, sum_price]

ans30 = Authors.objects.all().filter(recommendedby__isnull=True)

one = Books.objects.all().filter(author__isnull=False)
two = Books.objects.all().filter(author__isnull=False, author__recommender__isnull=True)
ans31 = [one, two]

ans32 = Books.objects.all().filter(author__pk=1).aggregate(Sum('price'))

ans33 = Books.objects.all().order_by('published_date').last().title

ans34 = Books.objects.all().aggregate(Avg('price'))

ans35 = Publishers.objects.filter(books__author__pk=1).aggregate(Max('popularity_score'))

ans36 = Authors.objects.filter(books__title__icontains='ab').count()

ans37 = Authors.objects.annotate(f_count=Count('followers')).filter(f_count__gt=216)

ans38 = Authors.objects.filter(joindate__gt=datetime.date(year=2014, month=9, day=20)).aggregate(Avg('popularity_score'))

ans39 = Books.objects.all().annotate(bk_count=Count('author__books')).filter(bk_count__gt=10).distinct()

ans40 = Books.objects.all().annotate(count_title=Count('title')).filter(count_title__gt=1)
```
