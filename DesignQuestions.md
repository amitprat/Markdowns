#### [PasteBin](https://medium.com/codex/designing-pastebin-77e6e86172eb)

Pastebin web service enable users to store plain text over the network and generate unique URLs to access the uploaded data. It is also used to share data over the network quickly, as users would just need to pass the URL to let other users see it.

![](https://miro.medium.com/max/1050/1*75CQltkeKHlpVm8nwTwpoA.png)

[PasteBin LeetCode](https://leetcode.com/discuss/interview-question/system-design/124804/Design-Pastebin)

######High Level Design

At a high level, the system needs an application server that can serve read and write request. Application server will store paste data on block storage. All the metadata related to paste and user will be stored into a database. At a high level, various cache servers and load balancer can be configured to improve performance and scalabilty of the system.

![](https://discuss.leetcode.com/assets/uploads/files/1517558990493-high-level-design-resized.png)

---

#### [TinyUrl](https://www.educative.io/courses/grokking-the-system-design-interview/m2ygV4E81AR)

createURL(api_dev_key, original_url, custom_alias=None, user_name=None, expire_date=None)

deleteURL(api_dev_key, url_key)

---

#### [Twitter System Design (GeeksForGeeks)](https://www.geeksforgeeks.org/design-twitter-a-system-design-interview-question/)

![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20200925115436/High-Level-Solution-for-Twitter-System-Design.png)

![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20200925115605/Fanout-System-Design-Twitter.png)

#### [Twitter System Design (Code Karle)](https://www.codekarle.com/system-design/Twitter-system-design.html)

![](https://www.codekarle.com/images/Twitter.png)

###### [Twitter Design (narendra L)](https://medium.com/@narengowda/system-design-for-twitter-e737284afc95)

###### [Infrastructure behind Twitter](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2017/the-infrastructure-behind-twitter-scale)

###### [Twitter Manhattan - A Key Value Store](https://blog.twitter.com/engineering/en_us/a/2014/manhattan-our-real-time-multi-tenant-distributed-database-for-twitter-scale)

---

#### [Spark Streaming vs Flink vs Storm vs Kafka Streams vs Samza : Choose Your Stream Processing Framework](https://medium.com/@chandanbaranwal/spark-streaming-vs-flink-vs-storm-vs-kafka-streams-vs-samza-choose-your-stream-processing-91ea3f04675b)

---

#### [Design Instagram (Educative)](https://www.educative.io/courses/grokking-the-system-design-interview/m2yDVZnQ8lG)

#### [System Design Analysis of Instagram](https://towardsdatascience.com/system-design-analysis-of-instagram-51cd25093971)

![](https://miro.medium.com/max/1500/1*hSfGyGyU4bhQAjPIdxAMJQ.jpeg)

---

#### [Other Important System Design Links]()

###### [Cracking System Design Interviews](https://www.linkedin.com/pulse/cracking-system-design-interviews-sreeja-das/)

---

https://blog.jupyter.org/interactive-workflows-for-c-with-jupyter-fe9b54227d92

https://medium.com/javarevisited/25-software-design-interview-questions-to-crack-any-programming-and-technical-interviews-4b8237942db0

https://www.quora.com/What-are-some-typical-design-interview-questions-for-software-engineers

https://www.geeksforgeeks.org/top-10-system-design-interview-questions-and-answers/

https://hackernoon.com/top-10-system-design-interview-questions-for-software-engineers-8561290f0444

https://www.educative.io/blog/uber-backend-system-design

https://medium.com/@narengowda/uber-system-design-8b2bc95e2cfe

https://www.geeksforgeeks.org/system-design-of-uber-app-uber-system-architecture/

https://medium.com/nerd-for-tech/understanding-redis-in-system-design-7a3aa8abc26a

https://leetcode.com/discuss/interview-question/125751/design-a-distributed-cache-system

https://leetcode.com/discuss/interview-question/system-design/1588415/WhatsAppMessaging-Chat-System-Design

https://leetcode.com/discuss/interview-question/system-design/1616482/System-Design%3A-Rate-Limiter

https://leetcode.com/discuss/interview-question/system-design/1577754/Facebook-or-Onsite-Product-Design-Interview-or-Design-Dropbox
