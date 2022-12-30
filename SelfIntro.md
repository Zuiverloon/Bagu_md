# tell me about your self

ZJY, applying for [POS] in your company.  
graduate from ecnu with a bachelor degree in cs, pursuing my postgrad at cuhk major in IE, expecting to graduate next summer.  
When I was an undergrad, 2 periods of internships.  
Summer vacation of 2021, at MEITUAN as a QA intern. Responsible for testing the functionality on both Web&Mobile apps. Writing auto testing code in Java and monitoring the testing result in Jenkins platform. reporting possible bugs to devs.

Summer of 2022, at Ctrip.com as a BE intern. Responsible for developing and maintaining backend APIs on Flight Ticket Refund Module and writing unit tests for APIs.

In my free time, I like watching sports games. To me, it is very inspiring. I enjoy coding as well. I can write code in Java,C++, familiar with MySQL DB. Sometimes I turn to Leetcode codeforce to solve problems and take part in the contest for fun.

# Internships

Meituan:
New feature: Timetable panel. Customers book a seat in a restaurant. Owner can see customer information on the timetable panel. (9:00 am several customers). If all seats are booked, customers can no longer book a seat at that time.
Several cases(no customers, several customers book the same time, several customers book different time. No seats to book).

CTrip:
New Feature: Add a Boolean field called useServicePackage to the simple refund apply interface. (Servicepackage is something similar to coupon.) If the value is true, we will refund more money if the customer has some coupons. In db, add a field in a table. When implementing this feature, first write the value into db, then if use, call another api to get how many coupons a customer has and the amount of the coupons. Calculate sum of the coupon amount and finally call function to proceed the refund.(fee calculation, validation, payment, generate a receipt or invoice)

**experience in Devops platform?**  
jenkins: link git repo to a jenkins project. run the testing case in the repo periodically by jenkins. We can see the result. If any errors occur, I can be informed and I will tell our developer to check.
CICD: when we push the code into the git branch, it will automatically trigger the testing. I can also manually trigger it if I would like to test some code. It can also help me to pack and deploy the package to maven repo.

## **Face with difficult task, how to deal with it?**

Build a compiler demo as the course project.  
Compilation theory is the most difficult course, building is more complicated.  
Have no idea how to begin.  
Firstly I went over everything on the book make sure I understand all.  
Turn to github to find some examples.  
After seeing several examples, I find there are some features in common. Different people follows the same pattern to build their own compiler. Lexical analysis -> grammar analysis -> generate intermidiate code -> execute instructions one by one.  
I followed this pattern and build every component one by one. When you get the idea, coding is always the easy part. Finally it works.

## **work on a team/make difficult decision**

undergrad, work with my roommate to build an online website as our course project. I was the team leader and had another 2 guys. We agree that everyone should be involved in this project, everyone should at least do something. I would like to develop the backend part(db management). At the same time,they were waiting for me to give them a task. It is always difficult to give instructions to roommates. So I asked them what they would like to be responsible for. Luckily one guy was willing to write the front end page and the other guy who is good at speaking, he was willing to prepare for the final presentation. Luckily we were responsible for different part, everyone had his own work to do. When implementing the project, we talk about the detail together, solve problem together and finally we put everything into a complete project.

## **Career plan for 5 years**

I plan to become an experienced software engineer in next 5 years. I should be able to manage the tasks in my work, avoid making bugs or mistakes, understand the general logic of my group project(why we need this module, how we implement the functions)  
If possible, I hope I can take part in designing our backend system, try to optimizing the efficiency to serve more requests. Designing work is quite important. Good designs and bad designs differ. For good designs, it can last for several year, running very efficiently.(TCP/UDP are good designs,decades ago, people use them and now we are using them ) For bad designs, maybe we need to revise it once a week which costs a lot of time and human resources.  
Generally speaking I would like to become a experienced or expert developer or analyst in 5 years time.

## **strength && weakness**

Patient. It is very common that we don’t understand something when we first meet it. If I am still in confusion, I will try to think about it once more and read documentations once again. Never give up halfway. If possible, I will raise some questions about it. I never get impatient or angry if met with problems. It’s good to learn something new.  
Brave to raise questions. Maybe a lot of people are scared of raising questions but I am always brave to raise questions. Once someone answer my questions, it is likely for me to get new ideas from their word and I will no longer being bothered by the problem. Raising questions is an effective and efficient way to solve the problem. Turning to someone for help I think is always more efficient than solving the problem by myself.

Stubborn. If I am faced with a problem, I will keep on solving it even if it is lunch time, even if I have spent a lot of time on it. I seldom leave the problem aside. Admittedly, it’s not very efficient. Maybe sometimes I should be more farseeing. If I spend too much time on a single problem, I should temporarily switch to another task and after I finish another task, I go back to fix this problem.

## Best achievement

1. When I worked at MEITUAN as QA, I give out a new way to test an api under concurrency situation(idempotent).
   Previously if we want to test the concurrency compatibility, we used to generate a threadpool to send request via several threads. It is a little bit complicated since we need to new a threadpool. Then I read the doc about our testing frame(testng), Surprisingly, I find that we can just write an argument in the annotation to implement this. We no longer need to maintain a threadpool,the code get shorter,the logic is easy to understand. save the time of all of my colleagues. From then on, my colleagues started to use my approach.(too technique) Generally speaking, I found a new feature of our testing framework that nobody knows before and the feature can save us a lot of time and energy to do testing.

2. I build a website for my mum to recite english words. My mun said she always forgot the english words learned last week. You can type english words in the website and it will be stored in the database. Users will be informed what he learned yesterday or in last week so it can help us to recite and review english words. Why I think it is an achievement is that my application is working in the real world and it is helpful to people. It's pretty meaningful. As a tech analyst, our duty is to build applications to serve our clients and bring convenient to people.
