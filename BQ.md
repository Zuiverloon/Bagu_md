# BQ

conflict / failure case / tight deadline / teamwork / 适应协调沟通

Situation Task Action Result

strength / stress / deadline /

## strength

**Patient**. when learning new skills, common cannot understand immediately.
think twice, read doc twice. deeper understanding. will not get angry and give up halfway. I start then keep working and successfully finish.

**Brave to raise questions**. common when face with questions or obstacles. if depend on myself, cost a lot of time energy, not efficient. to colleague or leader for help. experts will give answer to question and know how to deal with possible problem in the future. Some people, shy. I not. like raising questions and share with colleagues.

**weak**  
Stubborn. coding with bug, I spend time debugging without moving forward to next. dont have dinner. sometimes it is not efficient. stop and get refreshed then likely to find bug.

I don like the agreement being broken. friends agree to go to the party,but suddenly one guy quit. I know that something happened suddenly you must quit, for me I still feel unconfortable.

## build a compiler (difficulty)

a compiler demo as the course project. compilation theory is most difficult, building more complicated. no idea how to begin.  
went over everything on the textbook make sure I understand all. github to find some examples. find there are some features in common. follows the same pattern to build their own compiler. split into lexical analysis -> grammar analysis -> generate intermidiate code -> execute instructions. follow the rule, divide into small part and complete one by one. having the idea, coding is always the easy part. put everything together and fix tiny bugs it works. when presenting my work to prof, prof want to give me some challenge. he asked me to change my code to fit some new grammar. my program have components, I only modify one component. do it quickly and show to professor. he said I did a great job.

## recruiting website(work in a team/difficult decision)

undergrad, work with my roommate to build an online website as our course project. I team leader and had another 2 guys. My teammates not quite active waiting for me to give them tasks. I hope everyone can be involved and everyone can enjoy. So I asked them what they would like to be responsible for to see how to make arrangement. Luckily one guy good at html was willing to write the front end page and the other guy who is good at speaking, he was willing to prepare for the final presentation. I backend db. everyone had his own work to do. When implementing the project, we talk about the detail together, solve problem together and finally we put everything into a complete project and the presentation and demonstration perfect. good memory for me.

## concurrency testing(teamwork / argument)

When I worked at MEITUAN as QA, our team once talked about how to test an api under concurrency situation(idempotent). send several identical requests in a short time. hope that only one request can be processed, others be blocked. One of my colleague said we can generate a threadpool to send request via several threads. I think complicated. Then I read the doc about our testing frame(testng), Surprisingly, I find that we can just write an argument in the annotation to implement this. We no longer need to maintain a threadpool,the code get shorter,the logic is easy to understand. save the time of all of my colleagues. two guys gave two opinions. we dont have experience before. seems to be a argument. our team talk about both solutions in a weekly meeting and everyone tried both solution. Most said mine better. finally our team take my solution and the other guy was convinced. We programmers we are rational, we are sharing rather than arguing.

## fpga board(tight deadline)

I with one of my roommate to do a embedded system project together. write code and put in fpga board to run(the project is about temperature monitoring. read tem from sensor. if the temperature is higher than threshold, the light on the board will turn red). due to the quota, the fpga board is shared among all students, each group will have several days to implement the project and make a presentation to prof. if start code after get board, then the schedule tight. we decided to start coding before receive the board. complicated challenging because cannot test on board. only read and compile. solution: write code and code review with each other make sure no manifest bugs, sometimes bugs in ifelse branches, forgot to reset var. save time for debugging on the board. luckily no bugs when running. we finish the lab, deliver a represent to prof, finish report.

## failure

Work at Meituan, test on a new feature. write testing cases, execute the test, report bugs. When doing test, ok.publish online, we meet with some bugs. write numbers into db, read number out print on the table. actually in db, this value is stored in string. and we have some obsolete string data in db (not number but abcstrings) when testing everything ok, I input numbers into.but in production env, bugs occurs. it doesnt lead to bad result. dev fix and republish. I learn that I should take all cases into consideration. even if inputbox only allowed to enter number, cannot assume data in db is numbers. still check whether data is compatible in db.

## what makes a good analyst?

1. critical thinking. We will face different problems in working. We should think about the problem in different angles, more comprehensively more deeply. We should not restrict our thinking. For example, we can analyze the pros and tradeoffs and finally find the optimal solution.
1. keep learning and be eager to new tech. Although we can never learn everything and maybe it cannot be applied in working immediately, sometimes when learning new skills, I come up with new ideas, creativity and new ideas are always welcomed and it will usually lead to progress. So Learning and obtaining new ideas are important.
1. passion and happiness. Love job. Love what we are doing. Enjoy my work. we are not robots. we are human beings full of emotions.

## **Career plan for 5 years**

I plan to become an experienced software engineer in next 5 years. I should be able to manage the tasks in my work, avoid making bugs or mistakes, understanding the general logic of my group project(what is the demand, why we need this module, how we implement the functions)
Besides, I would like to take more responsibility in designing the whole system. system design is important. Good & bad differs. For good design, it can save a lot of time,improve working efficiency. Bad design, we need to upgrade once a week, do replicated job.
build both backend(focus) and frontend. if application can be popular, front page is necessary.
