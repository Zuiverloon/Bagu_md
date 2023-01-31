# tell me about your self

ZJY, come from SH,China. Nice to meet you and happy to have the opportunity to be here having interview with you.
graduate from ecnu with a bachelor degree in cs, pursuing my postgrad at cuhk major in IE, expecting to graduate this summer.  
When I was an undergrad, 2 periods of internships.  
Summer vacation of 2021, at MEITUAN as a QA intern. Responsible for testing the functionality on both Web&Mobile apps. Writing auto testing code in Java and monitoring the testing result in Jenkins platform. reporting possible bugs to devs.

Summer of 2022, at Ctrip.com as a BE intern. Responsible for developing and maintaining backend APIs on Flight Ticket Refund Module and writing unit tests for APIs.

In my free time, I like watching sports games. To me, it is very inspiring. I enjoy coding as well. Everytime I successfully write code to solve practical problems, it brings me a lot of happiness. That is why I d like to start my career as a developer cuz I want coding to be a major part of my work in my whole career.

## how to describe a project?

1. 这个项目是干什么的？
2. 为什么用了这个项目和框架
3. 架构（用了哪些 component？）
4. 文件/文件夹，库？
5. 基本的工作流

# Internships

Meituan:  
**Project**  
Our group was responsible for testing both web and mobile application for shop and restaurant owners to check the information of the customers who made reservations in that resaurant. We perform both manual testing and auto testing. For the auto testing part, we write code in java under TESTNG framework and periodically run the code in Jenkins platform to see whether there are some errors reported. For me, I participated in chaos engineering project to test the robustness of the system with some colleagues.

**chaos engineering**  
it is not our daily work but part of my colleagues and I participate in and try to learn and find out the best way to do chaos engineering. The general is firstly we need to manage the chaos in a devops platform called monkey. Chaos(db failure, redis failure, api timeout). Of course we write code in Java TESTNG framework. When writing code, we need to explicitly inject the failure to the system by calling api. Execute the test functions to see whether the system can perform without errors. Finally we need to recover the system, remove the chaos. We have independent environment for testing so it will not influence the production.

**New feature**  
Timetable panel. Customers book a seat in a restaurant. Owner can see customer information on the timetable panel. (9:00 am several customers). If all seats are booked, customers can no longer book a seat at that time.
Several cases(no customers, several customers book the same time, several customers book different time. No seats to book).

**writing auto test case**
provide some input output case.
call the function with input.
assert whether the output matched the expected output. If not matched, the framework will help to throw out exceptions. check the log.

**any difficulty?**  
We have plenty of devops platform(chaos management/cicd/jenkins/service registration) in the company. I need some time to get familiar with that platforms. I think I am a quick learner. Everytime my colleagues show me how to deal with that platform, then I understand. Just need time for learning.

CTrip:  
退票流程：refundapply, validation, feecalculation, send the request to alirline company, voucher/receipt

**Project in CTrip**  
Our group was responsible for developing flight ticket refund module. I participated in developing new features in SimpleRefundApply api by which the call center can refund the ticket for customer. We only focus on backend system. We develop the system in java with spring framework and mysql db and we write unit test in groovy. BTW We did agile development in our daily working.

**Agile**
we have a short meeting every morning, everyone talking about what he did yesterday and what he will do today. We regard two weeks as a sprint, pm will give tasks, all tasks will be put on the timetable in a web page and assigned to the corresponding developers. we keep track of everything about the task in the system eg: how many days we expect to spend, how many days we actually spend, the doc link. Especially if we cannot proceed the work due to some issue, we should explicitly block the task and wait for pm to solve. That is the daily routine about the agile.

**new feature**  
for SimpleRefundApply API. The params are simple because the API is used by call center so they dont have to time fill in all the params. Use limited input params to query all the information needed for processing the refund and store the data in our database. We will not process the refund instantly, instead, have another periodic job to load the data from db and call the refundprocess function to process the accumulated refund requests. The new feature is I add a new boolean field called useCoupon. If the value is true, then we need to pay back more money. Besides, I also need to modify the periodic job because it need to read one more field and calculate how much coupon the customer has. The result will be put into the refundprocess api for further procedures.  
**为什么不即时退票** because those who make a phone call to call center to refund the ticket didnt expect to get the result immediately. They will shortly ring off. We dont need to process it immediately.

**any difficulty**
at the beginning, I was not familiar with the business logic and the code in our group. In the first week of my internship, I read the doc to learn the whole procedure of module. When I participate in the development, I found it difficult to get data that I need because so many tables,field. I ask my colleague he told me we have encapsulated methods to get data. As long as I know which method to call, the work get easier.

**experience in Devops platform?**

1. jenkins(run scheduled tasks): link git repo to a jenkins project. run the testing case in the repo periodically by jenkins. We can see the result. If any errors occur, I can be informed and I will tell our developer to check.
2. CICD(continuous integration/deployment): when we push the code into the git branch, it will automatically trigger the testing. I can also manually trigger it if I would like to test some code. It can also help me to pack and deploy the package to maven repo.

## personal project

tiny web applications to solve my daily problem.

1. web application write down and show all my daily expenses.how much I spend, on what day, what did I buy to help keep track of my expenses. Data is stored in local mysql server. The front end framework is vue, the backend is springboot + mybatis(connect to db).
   1. **why vue** it should be flexible,interact with backend server so js framework may help.
   1. **why springboot** easily build java restapi applications,receive http from frontend.
   1. **mybatis** no need to care about the connection. If using jdbc, copying data from result into java object.
   1. **mysql** one expense is a row of data. (how much, date, what I buy)
   1. **why build this project** need a tool to monitor my daily expense. write different sql query to filter the data as I wish. learn framework like vue to have fun.

## **Face with difficult task, how to deal with it?**

Build a compiler demo as the course project.  
Compilation theory is the most difficult course, building is more complicated.  
Have no idea how to begin.  
Firstly I went over everything on the book make sure I understand all.  
Turn to github to find some examples.  
After seeing several examples, I find there are some features in common. Different people follows the same pattern to build their own compiler. Lexical analysis -> grammar analysis -> generate intermidiate code -> execute instructions one by one.  
I turn to an example, imitate the example and followed this pattern and build every component one by one. When you get the idea, coding is always the easy part. Finally it works.

## **work on a team/make difficult decision**

1. undergrad, work with my roommate to build an online website as our course project. I was the team leader and had another 2 guys. My teammates were waiting for me to give them tasks so that we can finish the project together. It is always difficult to give instructions to roommates. So I asked them what they would like to be responsible for to see how to make arrangement. Luckily one guy was willing to write the front end page and the other guy who is good at speaking, he was willing to prepare for the final presentation. Luckily we were willing to responsible for different part, everyone had his own work to do. When implementing the project, we talk about the detail together, solve problem together and finally we put everything into a complete project and it was done very successfully.
2. When I worked at MEITUAN as QA, our team once talked about how to test an api under concurrency situation(idempotent). We want to test the concurrency compatibility of our system. One of my colleague said we can generate a threadpool to send request via several threads. It is a little bit complicated since we need to new a threadpool. Then I read the doc about our testing frame(testng), Surprisingly, I find that we can just write an argument in the annotation to implement this. We no longer need to maintain a threadpool,the code get shorter,the logic is easy to understand. save the time of all of my colleagues. From then on, my colleagues started to use my approach.(too technique) Generally speaking, I found a new feature of our testing framework that nobody knows before and the feature can save us a lot of time and energy to do testing. I think it is good teamwork, we talk about different solutions and find the optimal one and adapt it in our daily working.

## **Career plan for 5 years**

I plan to become an experienced software engineer in next 5 years, still working in tech area because I like tech. I should be able to manage the tasks in my work, avoid making bugs or mistakes, understand the general logic of my group project(what is the demand, why we need this module, how we implement the functions)  
If possible, I hope I can take part in designing our backend system, try to optimizing the efficiency to serve more requests. Designing work is quite important. Good designs and bad designs differ. For good designs, it can last for several year, running very efficiently.(TCP/UDP are good designs,decades ago, people use them and now we are using them ) For bad designs, maybe we need to revise it once a week which costs a lot of time and human resources.  
Generally speaking I would like to become a experienced or expert developer or analyst in 5 years time.

## **strength && weakness**

Patient. It is very common that we don’t understand something when we first meet it. If I am still in confusion, I will try to think about it once more and read documentations once again. Never give up halfway. Everytime I read the book twice, I understand more about what I have read. If possible, I will raise some questions about it. I never get impatient or angry if met with problems. It’s good to learn something new.  
Brave to raise questions. Maybe a lot of people are scared of raising questions but I am always brave to raise questions. All of our colleagues are experienced. Once someone answer my questions, it is likely for me to get new ideas from their word and I will no longer being bothered by the problem. Raising questions is an effective and efficient way to solve the problem. Turning to someone for help I think is always more efficient than solving the problem by myself.

WEAK  
Stubborn. If I am faced with a problem, I will keep on solving it even if it is lunch time, even if I have spent a lot of time on it. I seldom leave the problem aside. Admittedly, it’s not very efficient. Maybe sometimes I should be more farseeing. If I spend too much time on a single problem, I should temporarily switch to another task and after I finish another task, I go back to fix this problem.

## Best achievement

1. When I worked at MEITUAN as QA, I give out a new way to test an api under concurrency situation(idempotent).
   Previously if we want to test the concurrency compatibility, we used to generate a threadpool to send request via several threads. It is a little bit complicated since we need to new a threadpool. Then I read the doc about our testing frame(testng), Surprisingly, I find that we can just write an argument in the annotation to implement this. We no longer need to maintain a threadpool,the code get shorter,the logic is easy to understand. save the time of all of my colleagues. From then on, my colleagues started to use my approach.(too technique) Generally speaking, I found a new feature of our testing framework that nobody knows before and the feature can save us a lot of time and energy to do testing.

2. I build a software with both frontend webpage and backend database for my mum to recite english words. My mum said she always forgot the english words learned last week. So I develop the software to solve this problem for her. The general idea is that We can type english words in the website and it will be stored in the database. Users can click buttons to show what he learned yesterday or in last week so it can help us to recite and review english words. Why I think it is an achievement is that this application is built by myself and working in the real world and it is helpful to people. It's pretty meaningful. As a tech analyst, our duty is to build applications to serve our clients and bring convenience to people. I am glad to see that my application help my mum in her daily life. A strong sense of achievement.

## what makes a good analyst?

1. critical thinking. We will face different problems in working. We should think about the problem in different angles, more comprehensively more deeply. We should not restrict our thinking. For example, we can analyze the pros and tradeoffs and finally find the optimal solution.
1. keep learning and be eager to new tech. Although we can never learn everything and maybe it cannot be applied in working immediately, sometimes when learning new skills, I come up with new ideas, creativity and new ideas are always welcomed and it will usually lead to progress. So Learning and obtaining new ideas are important.
1. passion and happiness. Love job. Love what we are doing. Enjoy my work. we are not robots. we are human beings full of emotions.

## 测试还是开发？

developer
testing is part of the duty as a developer.
Directly take part in building software. Although it is challenging, but it is inspiring as well. Glad to see my application can work well in the real world and bring convenience to people.

## 会接受 offer 吗？

So far I dont have any offer. If you give, 1st. I will appreciate it and definitely accept the offer. No one can reject such a big name comapny. If I get the opportunity to join you, I will not take other companies into consideration.
