**Overall Goal:** Evaluate the candidate's system design proficiency, focusing on scalability, reliability, maintainability, trade-off analysis, and problem decomposition for complex backend systems.

**My Approach (as Jacob):**
*   Be direct and clear about the problem and expectations for each round.
*   Focus on the candidate's thought process, not just the final answer. There are often multiple valid solutions.
*   Probe deeply into their choices and the reasoning behind them (the "why").
*   Present trade-offs and challenge assumptions to gauge their depth of understanding.
*   Share alternative approaches where relevant, but ultimately assess *their* design and justification.
*   If requirements are missing, I expect the candidate to ask clarifying questions. If they don't, I will prompt them, but note it as an area for feedback.

---

**Interview Itinerary (3 Rounds, ~45-60 minutes each):**

**Round 1: Foundational Design & Breadth**

*   **Objective:** Assess core system design skills, requirement gathering, API design, data modeling, and handling of moderate scale.
*   **Problem:** "Design an Activity Feed System." (Similar to a simplified Twitter timeline, Facebook news feed, or activity log). Users can post short updates, follow other users, and see a chronological feed of updates from users they follow.
*   **Key Areas to Probe:**
    *   **Requirements Clarification:** What features are essential (posting, following, feed retrieval)? Functional and Non-functional requirements (scale estimates - users, posts/sec, reads/sec; latency expectations, consistency needs).
    *   **API Design:** Key endpoints (e.g., `POST /posts`, `POST /follow`, `GET /feed`). Request/response formats. Authentication/Authorization.
    *   **Data Modeling:** How to store users, posts, follows, feeds? Choice of database(s) (SQL vs. NoSQL, why?). Schema design. Indexing strategy.
    *   **High-Level Architecture:** Core components (Web Servers, Application Servers, Databases, Caching?).
    *   **Feed Generation Logic:** How is the feed composed when a user requests it (read-time fan-out) vs. pre-computed (write-time fan-in)? Trade-offs?
    *   **Basic Scaling & Bottlenecks:** Initial thoughts on handling load, potential bottlenecks (e.g., hot users, database load). Use of caching (where, what, why?).
*   **My Focus:** Can the candidate define the problem scope, design reasonable APIs and data models, and discuss the fundamental trade-offs, especially read vs. write strategies for feed generation?

**Round 2: Scaling, Complexity & Domain Relevance**

*   **Objective:** Dive deeper into scaling challenges, specific component design, handling large datasets, and potentially leverage domain knowledge (drawing from dating app experience).
*   **Problem:** "Design a User Matching Service for a Dating App." The core function is to find potential matches for a user based on criteria like location, age range, preferences, and potentially some basic compatibility score. Focus on the backend service that powers the "discovery" or "swipe" feature.
*   **Key Areas to Probe:**
    *   **Requirements Clarification:** Specific matching criteria? Real-time location importance? How often do profiles/preferences change? Scale (millions of users, concurrent users)? Latency requirements for finding matches? Consistency (is slightly stale data acceptable?).
    *   **API Design:** Endpoint for requesting matches. How are criteria passed? Pagination?
    *   **Data Modeling & Storage:** Storing complex user profiles with various attributes. Handling geospatial data efficiently (Geo-indexing). Database choice(s) and justification (e.g., relational for core profile, Elasticsearch/PostGIS for search/geo, NoSQL for flexibility?).
    *   **Matching Algorithm (High Level):** How would the system efficiently filter millions of users based on multiple criteria? Discussion of indexing strategies beyond basic DB indexes (e.g., inverted indexes, geo-hashes).
    *   **Scalability & Performance:** How to handle load, especially read-heavy patterns? Caching strategies (user profiles, query results?). Dealing with geographic hotspots (dense cities). Sharding strategies?
    *   **System Architecture:** Interaction with other services (User Profile Service, Location Service). Asynchronous processing needs?
*   **My Focus:** Can the candidate handle complexity and large scale? How do they approach geospatial challenges? Can they justify database/technology choices based on specific requirements (latency, query patterns, data types)? Can they discuss trade-offs related to consistency vs. performance in this context?

**Round 3: Real-world Operations, Reliability & Evolution**

*   **Objective:** Assess understanding of operational concerns, fault tolerance, asynchronous systems, and system evolution.
*   **Problem:** "Design a Push Notification System for delivering match alerts, messages, and promotional content." Users should receive timely notifications on their devices even if they aren't actively using the app.
*   **Key Areas to Probe:**
    *   **Requirements Clarification:** Types of notifications? Delivery guarantees (at-least-once, exactly-once)? Latency expectations? Scale (notifications per second/day)? Personalization? Opt-out mechanisms?
    *   **High-Level Architecture:** Components involved (API gateway, Notification Service, Message Queue, Worker Fleet, Database for tracking state/tokens, connections to APNS/FCM).
    *   **Asynchronous Processing:** Role and choice of Message Queue (Kafka, RabbitMQ, SQS, etc.)? Why that choice? Handling backpressure.
    *   **Reliability & Fault Tolerance:** How to handle failures (e.g., worker crashes, downstream notification provider failures)? Retry mechanisms? Idempotency (avoiding duplicate notifications). Dead-letter queues.
    *   **Scalability:** Scaling the queue, the workers, database interactions. Managing device tokens efficiently.
    *   **Monitoring & Alerting:** What key metrics would you monitor? How would you detect issues? Logging strategy.
    *   **Operational Considerations:** Deployment strategies? Handling updates to notification logic? A/B testing notifications?
    *   **(Optional Stretch):** How might this system evolve to handle scheduled notifications or targeted campaigns?
*   **My Focus:** Does the candidate think beyond the basic components and consider real-world operational issues? Can they design for failure? Do they understand asynchronous patterns and the trade-offs between different queuing systems? How do they approach observability?

---

**Roleplay: Round 1 - Activity Feed System**

**(Scene: Virtual Interview Room)**

**Jacob (Interviewer):** Hi Alex, I'm Jacob Jacobson, Senior Staff Backend Engineer here. Thanks for joining today. As discussed, we'll be doing a few system design rounds. This first round focuses on foundational design and breadth. The problem is to design an Activity Feed system, like a simplified Twitter or Facebook feed. Users can post short updates, follow other users, and see a chronological feed of updates from users they follow. How would you approach designing this?

**Alex (Candidate):** Hi Jacob, thanks for having me. Okay, an Activity Feed system. Before diving into components, I'd like to clarify the requirements and scope.

*   **Functional Requirements:**
    *   Users can post updates (let's assume text initially? Any media like images/videos?).
    *   Users can follow/unfollow other users.
    *   Users can view a feed containing posts from users they follow, generally in reverse chronological order.
    *   Are there other interactions? Liking posts, comments, retweets/reposts? Let's start simple: posts, follows, feed view.
*   **Non-Functional Requirements:**
    *   **Scale:** How many users are we expecting? Daily Active Users (DAU)? How many posts per second? How many feed reads per second? This impacts architecture choices significantly.
    *   **Latency:** What's the acceptable latency for posting an update? More importantly, what's the latency expectation for loading the feed (e.g., p99 < 500ms)?
    *   **Consistency:** How critical is it that a user sees a new post *immediately* after it's made? Is eventual consistency acceptable for the feed? What about the follow action – should that be immediate?
    *   **Availability:** The system should be highly available, especially reading the feed.

**Jacob (Interviewer):** Good questions. Let's scope it initially:
*   **Functional:** Yes, text posts only for now. Max length, say 280 characters. Core functions are Post, Follow, and Get Feed. Likes/comments are out of scope for now.
*   **Non-Functional:**
    *   **Scale:** Let's design for 10 Million DAU. Assume peak traffic might be around 100 posts/second, but significantly more feed reads, say 10,000 reads/second (feeds are read much more often than posts are created). A user follows maybe 100 others on average.
    *   **Latency:** Feed load latency is critical. Let's target p99 < 500ms. Posting can take a bit longer, maybe a few seconds is acceptable.
    *   **Consistency:** This is a key trade-off. Let's assume for feed viewing, eventual consistency within, say, 5-10 seconds is acceptable. Seeing a post *slightly* late is better than a slow feed load. Follow/unfollow actions should ideally feel near real-time, but a few seconds delay is okay too.
    *   **Availability:** Yes, aim for high availability (e.g., 99.9%).

Based on that, what's your high-level approach?

**Alex (Candidate):** Okay, thank you. The high read volume (10k QPS) vs. lower write volume (100 QPS) and the relaxed consistency requirement for the feed strongly suggest optimizing for read performance. This points towards pre-calculating or fanning-out posts to followers' feeds when they are created, rather than calculating the feed every time a user requests it.

So, the two main approaches for feed generation are:

1.  **Fan-out on Read:** When a user requests their feed, query all the people they follow, fetch their recent posts, merge, sort, and return.
    *   *Pros:* Simple write path (just save the post). Always consistent (reads the latest).
    *   *Cons:* Very expensive reads, especially if a user follows many people. High latency for feed loads. Doesn't meet our 10k QPS read and <500ms latency goals easily.
2.  **Fan-out on Write:** When a user posts, find all their followers and insert the new post (or a reference to it) into each follower's feed data structure. When a user requests their feed, simply read this pre-computed list.
    *   *Pros:* Extremely fast reads, meets latency goals. Scales well for reads.
    *   *Cons:* Writes are more complex and heavy. Potential for "hotspots" if a user with millions of followers posts (the "celebrity problem"). Requires more storage for the pre-computed feeds. Eventual consistency model.

Given the requirements (high read QPS, low latency feed read), I'd lean towards the **Fan-out on Write** approach. We'll need strategies to manage the write complexity and potential "celebrity" issues, but it best serves the read performance goal.

My proposed high-level components would be:
*   **Load Balancers:** Distribute incoming traffic.
*   **API Gateway:** Central entry point, handles auth, routing.
*   **Web/Application Servers:** Handle HTTP requests, coordinate backend calls.
*   **Post Service:** Handles creating, storing, retrieving individual posts.
*   **User/Follow Service:** Manages user profiles and the follow graph (who follows whom).
*   **Feed Service:** Responsible for the fan-out logic on write and serving the pre-computed feeds on read.
*   **Databases:** Likely multiple types – perhaps SQL for structured user/follow data, and maybe a NoSQL store or cache optimized for the feed lists.
*   **Message Queue (Potentially):** To decouple the fan-out process from the initial post creation, making posts faster and improving reliability of the fan-out.

**Jacob (Interviewer):** Makes sense. You chose Fan-out on Write, justifying it based on the read-heavy workload. Let's dig into the APIs and data models. What would the key APIs look like, and how would you store the data, particularly for users, follows, posts, and the feeds themselves using this approach? What database choices are you considering?

**Alex (Candidate):** Sure.

**API Design:** (Assuming RESTful APIs)

*   `POST /v1/posts`
    *   Auth: Required (e.g., JWT token)
    *   Body: `{ "text": "My first post!" }`
    *   Response: `201 Created`, Body: `{ "post_id": "...", "user_id": "...", "text": "...", "timestamp": "..." }`
*   `POST /v1/users/{user_id}/follow`
    *   Auth: Required
    *   Response: `202 Accepted` or `200 OK`
*   `DELETE /v1/users/{user_id}/follow`
    *   Auth: Required
    *   Response: `204 No Content` or `200 OK`
*   `GET /v1/feed`
    *   Auth: Required
    *   Query Params: `?limit=20&cursor=nextToken` (for pagination)
    *   Response: `200 OK`, Body: `{ "posts": [ { "post_id": "...", "user_id": "...", "text": "...", "timestamp": "..." }, ... ], "next_cursor": "..." }`

**Data Modeling & Storage:**

1.  **Users Table:** Stores user profile info.
    *   Columns: `user_id (PK)`, `username`, `name`, `created_at`, etc.
    *   Database: A relational database (like PostgreSQL or MySQL) makes sense here due to structured data and potential relational queries (though maybe not many in this simplified version). It ensures ACID compliance for profile updates.
2.  **Follows Table:** Stores the relationship graph.
    *   Columns: `follower_id (FK to Users)`, `following_id (FK to Users)`, `created_at`. Primary Key `(follower_id, following_id)`.
    *   Database: Relational DB again. Need efficient lookups:
        *   Find all users someone *is following*: `WHERE follower_id = ?` (Index on `follower_id`)
        *   Find all *followers* of a user: `WHERE following_id = ?` (Index on `following_id`)
3.  **Posts Table:** Stores the actual post content.
    *   Columns: `post_id (PK)`, `user_id (FK to Users)`, `text_content`, `created_at`.
    *   Database: Could be SQL or NoSQL. If SQL, easy relation to Users. If NoSQL (like Cassandra or DynamoDB), might offer better scalability for writes/reads of individual posts, partitioned by `user_id` or maybe even `post_id` (if using UUIDs). Let's start with SQL for simplicity unless scale forces us otherwise. Index on `user_id` and `created_at`.
4.  **Feed Storage:** This is crucial for the Fan-out-on-Write approach. We need to store a list of `post_id`s for each user's feed.
    *   Structure: Essentially a mapping: `user_id -> List<post_id>` (sorted by time).
    *   Database/Store: This screams for a system optimized for key-value access where the value is a list/collection.
        *   **Redis (Sorted Sets or Lists):** Excellent choice. Redis Sorted Sets could store `post_id`s with timestamps as scores, allowing easy time-based retrieval and trimming. Lists could work too, but sorting might be trickier. It's in-memory, so very fast reads. Persistence can be configured. We'd likely need sharding.
        *   **NoSQL Document Store (like MongoDB):** Could store a document per user containing their feed list. Less ideal for very large lists within a single document.
        *   **Wide-column NoSQL (like Cassandra):** Could model this as `(user_id, timestamp) -> post_id`. Very scalable writes and reads based on `user_id`.

    *   My choice: **Redis Sorted Sets** seem like a strong fit initially due to the speed, native list operations, and sorting capabilities. We store `user_id` as the key, `timestamp` as the score, and `post_id` as the value. We only store post IDs in the feed; the application layer hydrates these IDs with full post content (from the Posts table or a cache) when constructing the `GET /v1/feed` response.

**Jacob (Interviewer):** Okay, using Redis Sorted Sets for the feed structure makes sense for fast reads. You mentioned the fan-out process potentially involves a message queue. Can you elaborate on how that would work when a user creates a post? And how does this fan-out approach handle the "celebrity problem" you identified earlier – someone with millions of followers posting?

**Alex (Candidate):** Right. The naive approach is: User posts -> Post Service saves post -> Post Service synchronously gets follower list from User/Follow Service -> Post Service loops through followers and updates each one's Redis feed list. This is slow and prone to failure.

**Using a Message Queue (e.g., Kafka, RabbitMQ, SQS):**

1.  **Post Creation:** User calls `POST /v1/posts`. The Post Service saves the post to the Posts table quickly.
2.  **Publish Event:** After successfully saving, the Post Service publishes an event like `{"post_id": "...", "user_id": "...", "timestamp": "..."}` to a message queue topic (e.g., `post_created`). This makes the initial API call return faster.
3.  **Feed Fan-out Workers:** A separate pool of workers (part of the Feed Service, or dedicated) consumes messages from this topic.
4.  **Process Event:** For each message, the worker:
    *   Retrieves the full list of `follower_id`s for the `user_id` from the User/Follow Service.
    *   Iterates through the `follower_id`s. For each follower, it adds the `post_id` to their feed structure in Redis (e.g., `ZADD user:{follower_id}:feed {timestamp} {post_id}`).
    *   Handles potential errors/retries.

**Handling the "Celebrity Problem":**

This fan-out can be massive for celebrities. If someone has 10M followers, the worker needs to perform 10M Redis writes, which can take time and overload Redis/workers. Strategies:

1.  **Asynchronous Processing:** The message queue already helps by making it async, preventing blocking the user's post request.
2.  **Worker Scaling:** We can scale the number of fan-out workers based on queue depth.
3.  **Shard Followers:** The User/Follow service could potentially shard follower lists. A worker might fetch followers page by page to avoid loading millions at once.
4.  **Optimize Redis Writes:** Use Redis pipelining to batch multiple `ZADD` commands in fewer network round trips.
5.  **Cap Fan-out / Hybrid Approach (More Complex):**
    *   For users with > N followers (e.g., > 10,000), maybe don't fan out to *everyone*. Instead, fan out only to a subset (e.g., most active followers) or none at all.
    *   When a *follower* of a celebrity requests their feed, the Feed Service retrieves their regular pre-computed feed *and* separately queries for recent posts from the celebrities they follow (merging these results at read time). This is a hybrid approach – mostly fan-out-on-write, but fan-out-on-read for celebrity posts. It balances write load vs. read complexity. This requires identifying "celebrities" and adds complexity to the read path, but might be necessary for extreme scale.
6.  **Rate Limiting:** Limit how often celebrities can trigger massive fan-outs.

I'd start with the basic async fan-out via a message queue and optimize Redis writes. If the celebrity problem is still severe, I'd explore the hybrid approach as a next step.

**Jacob (Interviewer):** Good breakdown of the async fan-out and potential solutions for the celebrity issue. Let's touch on basic scaling and bottlenecks. Beyond the celebrity problem, what other bottlenecks do you foresee, and how would you introduce caching?

**Alex (Candidate):** Other potential bottlenecks and caching:

1.  **Database Hotspots (Posts/Users/Follows):**
    *   **Posts Table:** If using SQL, write contention could occur. Read load for hydrating feed posts could be high.
        *   *Caching:* Cache fully hydrated Post objects (Post ID -> Post Data) in something like Memcached or Redis. Use cache-aside pattern. TTL based on expected edit frequency (low for posts).
    *   **Users Table:** High read load for author details during feed hydration.
        *   *Caching:* Cache User profile data (User ID -> User Profile).
    *   **Follows Table:** High read load for getting follower lists during fan-out. High read load for getting following lists if we implemented the hybrid approach.
        *   *Caching:* Cache follower lists (especially for celebrities) or frequently accessed lists. Be mindful of cache invalidation when follows/unfollows happen. Could use a cache with a short TTL or event-based invalidation.
    *   *Scaling:* If caching isn't enough, consider read replicas for the SQL DB, or potentially sharding the tables based on `user_id`.
2.  **Feed Service Workers:** Can become a bottleneck if fan-out tasks build up.
    *   *Scaling:* Auto-scale the worker pool based on queue length or processing latency.
3.  **Redis Feed Store:** High write traffic during fan-out, high read traffic for `GET /feed`.
    *   *Scaling:* Shard Redis based on `user_id`. Use Redis Cluster or manual sharding via the application logic/proxy. Ensure enough memory and network bandwidth.
4.  **API Layer / Web Servers:** Standard stateless services, should scale horizontally easily behind a load balancer.

Essentially, aggressive caching for frequently accessed, relatively static data (post details, user profiles) is key to reducing database load. The feed data itself in Redis is already a form of pre-computation/cache. The main scaling challenges are handling the fan-out write amplification and scaling the underlying data stores (SQL DB, Redis).

**Jacob (Interviewer):** Okay, Alex. That covers the main areas I wanted to touch on for this round - requirements, high-level design, API/data modeling, the core feed generation trade-off, and basic scaling/caching. We discussed the fan-out-on-write approach, using Redis for feeds, async processing via queues, handling celebrities, and caching strategies. Do you have any questions for me about this part?

**Alex (Candidate):** Thanks, Jacob. That was a good discussion. Just one question - regarding the hybrid approach for celebrities, in your experience at places like Match Group or Netflix dealing with large-scale social graphs or recommendations, how often does that complexity become truly necessary compared to optimizing the pure fan-out-on-write path? Is it a common pattern or more of an edge-case optimization?

**Jacob (Interviewer):** That's a sharp question. In my experience, it depends heavily on the actual distribution of connections (followers, matches, etc.). For systems with a very steep power-law distribution (a few nodes with vastly more connections than others), the pure fan-out model *can* break down operationally under load, making the hybrid approach necessary despite its complexity. Optimizing the pure fan-out gets you very far, but often, for truly massive scale like major social networks or high-engagement platforms, some form of hybrid model or specialized handling for high-degree nodes becomes almost unavoidable. It's less common for smaller or more evenly distributed graphs.

---

**Roleplay: Round 2 - User Matching Service**

**(Scene: Virtual Interview Room, continuing the interview)**

**Jacob (Interviewer):** Alright Alex, welcome back for the second round. This time, we'll focus more on scaling, handling complexity, and leveraging domain-specific knowledge. The problem is to design the core **User Matching Service** for a dating app. This service is responsible for finding potential matches for a user when they open the app, powering something like the main discovery or swipe feature. How would you approach designing this?

**Alex (Candidate):** Hi Jacob, thanks. Designing a User Matching Service sounds interesting and challenging. Similar to the last round, I want to start by understanding the key requirements.

*   **Functional Requirements:**
    *   What are the primary matching criteria? I assume basics like age range, desired gender(s), location/distance are key. Are there others? Preferences (e.g., looking for relationship vs. casual), interests, education, maybe even some calculated compatibility score?
    *   How is location handled? Is it based on the user's current real-time location, or a set home location? How precise does the distance filtering need to be?
    *   How are matches presented? Is it an ordered list? How many matches should we aim to find per request?
    *   Do we need to exclude users already swiped on (left or right) or already matched with?
*   **Non-Functional Requirements:**
    *   **Scale:** How many active users are searching for matches concurrently? Total user base size? Let's assume similar scale to before, maybe 10s of millions total users, perhaps 1 Million concurrently active users during peak times requesting matches.
    *   **Latency:** Match discovery needs to feel fast. What's the target latency for returning a batch of potential matches (e.g., p99 < 1 second)?
    *   **Freshness/Consistency:** How up-to-date does the information need to be? If a user changes their location or age preference, how quickly should that reflect in the matches they *receive*, and how quickly should it reflect in whether *they appear* as a match for others? Is near real-time necessary, or is a delay of a few minutes acceptable?
    *   **Availability:** High availability is crucial; if users can't find matches, the app is essentially down.

**Jacob (Interviewer):** Excellent questions. Let's define those:
*   **Functional:**
    *   Criteria: Must include **age range**, **gender preference(s)** (user's preference matching potential match's gender), and **maximum distance** from the user's **current location**. Let's also include filtering based on who the user has **already interacted with** (swiped left/right, matched). Basic interest overlap might be a future goal, but let's keep it simpler for now. Compatibility scores are out of scope for this core service.
    *   Location: Use the user's **current reported location**. Assume updates come frequently when the app is active. Precision: Need to filter by distance accurately (e.g., within 50 miles/80 km).
    *   Presentation: Return a batch of, say, 20 potential matches per request. Order can be based on proximity, or maybe last active time, but simple proximity ordering is fine to start.
    *   Exclusions: Yes, definitely exclude users already swiped on or matched with by the requester.
*   **Non-Functional:**
    *   **Scale:** 50 Million total users, 1 Million concurrent users active. Assume maybe 1000 match requests/second peak.
    *   **Latency:** Target p99 < 1 second for returning a batch of 20 matches.
    *   **Freshness:** Let's aim for reasonably fresh data. Location updates reflecting within 1-2 minutes is acceptable. Preference changes should ideally reflect within a similar timeframe. Users appearing/disappearing from potential matches due to these changes within minutes is fine.
    *   **Availability:** Yes, target high availability (e.g., 99.95%).

Given this, what's your proposed architecture and how would you tackle the core challenges, particularly efficient filtering at scale?

**Alex (Candidate):** Okay, thank you. The key challenges are the large user base, the need for geospatial querying ("users within X distance"), filtering on multiple attributes (age, gender, exclusions), and doing this with low latency and reasonable freshness.

**High-Level Architecture:**

1.  **API Gateway/Load Balancer:** Entry point for requests.
2.  **Matching Service:** The core service handling match discovery requests. Stateless, horizontally scalable.
3.  **User Profile Service:** A separate service (or database access layer) managing core user data (age, gender, preferences, profile details). Assume this exists.
4.  **Location Service:** A service tracking user's current locations (latitude, longitude). Updates frequently. Assume this exists.
5.  **Interaction Service:** A service tracking swipes (left/right) and existing matches between users. Assume this exists.
6.  **Search/Query Engine:** A specialized system optimized for handling complex queries across the large user base, especially geospatial and multi-attribute filtering. This is likely the crux of the system.
7.  **Databases:** Underlying storage for user profiles, locations, interactions. Might involve different types.
8.  **Cache:** Aggressive caching for frequently accessed data (user profiles, possibly recent query results).

**API Design (Simplified):**

*   `GET /v1/matches`
    *   Auth: Required (identifies the requesting user)
    *   Query Params: `?limit=20&cursor=nextToken` (pagination if needed, although often dating apps show a finite set per session)
    *   Response: `200 OK`, Body: `{ "matches": [ { "user_id": "...", "name": "...", "age": ..., "distance_km": ..., "profile_summary": "..." }, ... ], "next_cursor": "..." }`

**Core Logic Flow:**

1.  Request hits Matching Service for User A.
2.  Matching Service gets User A's current profile (age, gender prefs), location, and recent interaction list (swiped/matched IDs) from relevant services/caches.
3.  Matching Service constructs a query for the Search/Query Engine:
    *   "Find users WHERE:"
        *   `location` is within X distance of User A's location.
        *   `age` is within User A's desired range.
        *   `gender` matches User A's preference.
        *   *Their* age/gender preferences are compatible with User A (reciprocity).
        *   `user_id` is NOT IN User A's exclusion list (swiped/matched).
        *   `is_active`/`profile_complete`, etc. (other basic filters)
    *   Sort by distance (or other criteria).
    *   Limit to N results (e.g., 20 + buffer).
4.  Search/Query Engine executes this complex query efficiently against the user data.
5.  Matching Service receives a list of candidate `user_id`s.
6.  (Optional) It might perform light re-ranking or final filtering.
7.  It fetches minimal profile details for the matched IDs (from cache or User Profile Service) to return in the response.
8.  Returns the list of matches.

**Jacob (Interviewer):** That makes sense. You've identified the need for a specialized Search/Query Engine. Let's dive into that and the underlying data storage. How would you store the user data – particularly the profiles with filterable attributes (age, gender) and the frequently updating location data? What technology would you choose for that Search/Query Engine and why?

**Alex (Candidate):** This is where careful technology selection is critical.

**Data Storage:**

1.  **Core User Profiles:** Attributes like `user_id`, `name`, `gender`, `birthdate` (for calculating age), `preferences` (age range, gender), account status, etc.
    *   *Storage:* A relational database (like PostgreSQL or MySQL) or a scalable document DB (like MongoDB) could work. Relational gives schema enforcement, but document DBs offer flexibility. Given the scale (50M users) and need for related data (preferences), sharding the chosen database (e.g., by `user_id`) would be necessary eventually. Let's assume PostgreSQL sharded by `user_id` for strong consistency on profile basics.
2.  **User Location:** `user_id`, `latitude`, `longitude`, `timestamp`. Updates frequently.
    *   *Storage:* Needs fast writes and efficient geospatial querying. Storing this directly in the main sharded PostgreSQL is possible using extensions like PostGIS, but might cause write contention and requires careful indexing. Alternatively, a specialized location database or cache (like Redis with Geospatial indexes, or even a dedicated Location Service with its own optimized store) could handle this better. Let's assume a dedicated Location store/service optimized for writes and geo-queries, possibly using Redis Geo commands or similar.
3.  **User Interactions:** `user_id_swiper`, `user_id_swiped`, `action` (left/right/match), `timestamp`. Very high write volume.
    *   *Storage:* Needs extremely high write throughput and fast lookups for exclusion lists (`WHERE user_id_swiper = ?`). A wide-column NoSQL store like Cassandra or ScyllaDB, partitioned by `user_id_swiper`, would be suitable. Storing interactions here avoids overloading the main user profile DB.

**Search/Query Engine:**

This engine needs to combine data from potentially different sources (profile attributes, location, potentially activity signals) and perform efficient filtering, including geospatial queries.

*   **Option 1: Database with Specialized Indexing (e.g., PostgreSQL + PostGIS):** If we keep location data in PostgreSQL with PostGIS, we could try to satisfy queries directly. We'd need composite indexes on `(gender, age, location_geo_index)` potentially.
    *   *Pros:* Keeps data potentially more consistent, fewer moving parts if it scales.
    *   *Cons:* Complex queries across potentially sharded tables can be slow. Geospatial indexing combined with other attribute filtering at this scale is challenging for traditional DBs. May not meet the <1s latency goal under heavy load.
*   **Option 2: Dedicated Search Engine (e.g., Elasticsearch, Apache Solr):** These are built for this kind of problem.
    *   *Process:* We'd need to continuously index relevant user data (ID, age, gender, location, activity signals) from the source databases (Postgres, Location Store) into Elasticsearch.
    *   *Querying:* Elasticsearch natively supports efficient geospatial queries (`geo_distance`), range queries (age), term filtering (gender), boolean combinations, and filtering by IDs (exclusions). It's designed for fast reads on complex filters.
    *   *Pros:* Highly scalable for search/filtering. Built-in relevance/scoring features (though we only need basic filtering now). Optimized for low-latency reads. Handles geospatial well.
    *   *Cons:* Adds operational complexity (managing an Elasticsearch cluster). Data is eventually consistent (lag between source DB update and index update). Requires robust data pipelines to keep the index fresh.

**Recommendation:** Given the scale (50M users, 1k QPS reads), the low latency requirement (<1s), and the mix of geospatial and attribute filtering, I would strongly recommend using a dedicated **Search Engine like Elasticsearch**. The benefits for query performance and flexibility outweigh the added complexity compared to trying to optimize complex queries on a sharded relational database. We would feed user profile and location updates into it asynchronously.

**Jacob (Interviewer):** Using Elasticsearch as the query engine is a common and solid pattern here. You mentioned indexing data into it asynchronously. How quickly does that need to happen to meet the 'freshness' requirement of ~1-2 minutes? And how do you handle the indexing load, especially for location updates which can be frequent?

**Alex (Candidate):** The indexing pipeline is critical.

*   **Freshness:** To achieve ~1-2 minute freshness, the end-to-end latency from a user updating their profile/location in the source database to it being searchable in Elasticsearch needs to be within that window. This involves:
    *   Change Data Capture (CDC): Using tools like Debezium to capture changes from the User Profile DB (Postgres) and Location Store commit logs.
    *   Message Queue: Publishing change events to a queue (e.g., Kafka).
    *   Indexing Workers: Consumers reading from Kafka, transforming the data into the Elasticsearch document format, and indexing it into Elasticsearch using bulk APIs for efficiency.
    *   Monitoring: Close monitoring of this pipeline's lag is essential.
*   **Handling Indexing Load (especially Location):**
    *   **Batching:** Indexing workers must use Elasticsearch's Bulk API to send batches of updates rather than individual documents per request. This significantly improves throughput.
    *   **Sampling/Throttling Location Updates:** Do we *really* need to index every single minor location fluctuation? Perhaps the Location Service could intelligently publish updates only when the location changes significantly (e.g., >100 meters) or after a certain time threshold (e.g., every 30 seconds if continuously moving). This reduces the indexing load.
    *   **Optimize Elasticsearch Indexing:** Tune Elasticsearch cluster settings (refresh interval, replica count during indexing, hardware provisioning) for write performance. The default `refresh_interval` of 1s makes changes searchable quickly but is expensive. We could relax it slightly (e.g., 5-10s, or even 30s) during bulk indexing, balancing freshness with performance, fitting within our few-minute overall goal.
    *   **Scalable Pipeline:** The Kafka topic and indexing worker pool must be scalable to handle peak load.

The goal is a robust pipeline that can keep up with change velocity, especially location updates, without sacrificing the target freshness or overwhelming Elasticsearch. Sampling location updates seems like a pragmatic optimization.

**Jacob (Interviewer):** Makes sense. Let's talk about performance under load. You have 1 Million concurrent users, potentially hitting this service frequently. How do you ensure low latency and availability? What are your caching strategies? How do you handle potential hotspots, like users in densely populated areas?

**Alex (Candidate):** Performance and availability are paramount.

1.  **Horizontal Scaling:** The Matching Service itself should be stateless and horizontally scalable behind a load balancer. Similarly, the Elasticsearch cluster needs sufficient nodes to handle the query load.
2.  **Caching:**
    *   **User Data for Query Construction:** Cache the requesting user's profile, preferences, location, and exclusion list (recently swiped IDs) within the Matching Service instances or a distributed cache (like Redis/Memcached). This avoids hitting source-of-truth services for every match request. TTLs should align with acceptable data freshness (e.g., 1-2 minutes).
    *   **Exclusion List Handling:** The list of users to exclude (`NOT IN (...)`) can get large. Instead of passing huge lists in the query, perhaps use Elasticsearch's capabilities better. One way is to use a boolean query combining a `must_not` clause with `terms` lookup for the excluded IDs. If the list is extremely large, more advanced techniques might be needed (e.g., Bloom filters, or indexing interaction data directly into the search engine - though that increases indexing load).
    *   **Query Result Caching (Cautiously):** Caching the *results* of a match query (`location + age + gender -> list of user IDs`) is tempting but tricky due to the constantly changing user locations and availability. The cache hit rate might be low unless queries are very frequently repeated *exactly*. A short TTL (e.g., 30-60 seconds) might provide some benefit for very common query patterns (e.g., popular age ranges in dense areas) but needs careful analysis. It might be more effective to ensure Elasticsearch itself is fast enough.
3.  **Handling Hotspots (Dense Areas):**
    *   **Geospatial Query Optimization:** Elasticsearch/PostGIS are generally good at this, but extremely dense areas can still be challenging. Ensure proper geo-indexing strategies are used (e.g., geohashes, quadtrees).
    *   **Geo-Sharding (Elasticsearch):** While Elasticsearch shards automatically, we could potentially influence sharding based on geo-location if hotspots become a major bottleneck. Routing queries for specific dense regions to dedicated nodes might be an advanced optimization. More commonly, simply ensuring the Elasticsearch cluster is adequately provisioned (memory, CPU, IO) is the first step.
    *   **Query Refinement/Limits:** Ensure queries always include reasonable distance limits. Avoid overly broad searches. The matching service might even dynamically adjust search radii if initial searches in dense areas return too many results too quickly, focusing on closer matches first.
    *   **Return Diverse Results:** Instead of just the absolute closest N users, maybe introduce some randomization or factor in other attributes slightly (like last active time) within the nearby set to avoid always showing the exact same neighbours.
4.  **Database Load Management:** Caching helps reduce read load. Ensure source databases (Postgres, Location Store, Interactions Store) have read replicas where appropriate and are properly scaled/sharded. Protect databases with connection pooling and circuit breakers from the Matching Service.

Focusing on a fast, well-resourced Elasticsearch cluster and effective caching of input data (user profile, exclusions) would be my primary strategies for meeting the latency and scale requirements.

**Jacob (Interviewer):** Good points on caching complexities and hotspot management. You've laid out a robust approach leveraging specialized tools like Elasticsearch for the core challenge.

Okay, Alex, this gives me a solid understanding of how you'd tackle this complex matching problem, integrating geospatial queries, multiple filtering criteria, and considering scale and performance. We're good for Round 2.

**(End Roleplay Round 2)**

---

**Roleplay Continuation: Round 2 - User Matching Service**

**Jacob (Interviewer):** Alex, your design using Elasticsearch seems well-suited for the filtering requirements. You mentioned one of the filters is **reciprocity** – User A must match User B's preferences, and User B's preferences must match User A. How would you implement that check efficiently within the Elasticsearch query itself, without needing excessive post-query filtering?

**Alex (Candidate):** That's a key detail for dating apps. We need to ensure we're not just finding people User A *might* like, but people who *might also* like User A back, based on their stated preferences (like age range and gender).

We can encode the user's *own* attributes (that others filter on) and their *preferences* (what they filter others by) within the same Elasticsearch document for each user. For example, a user document might look like:

```json
{
  "user_id": "user123",
  "gender": "woman",
  "birthdate": "1995-06-15", // Calculate age on the fly or index age directly
  "age": 28, // Example calculated age
  "location": { "lat": 40.7128, "lon": -74.0060 },
  "preferences": {
    "interested_in_gender": ["man"],
    "min_age": 27,
    "max_age": 35
  },
  "last_active": "2023-10-27T10:00:00Z",
  // ... other fields like indexed interactions if needed
}
```

Now, when User A (let's say, a 30-year-old man interested in women aged 28-38, located at specific coordinates) makes a request, the Matching Service constructs an Elasticsearch query that includes filters for both directions:

1.  **Filtering Potential Matches based on User A's Preferences:**
    *   `location` within X distance of User A.
    *   `age` between 28 and 38 (User A's preference).
    *   `gender` is "woman" (User A's preference).
    *   `user_id` NOT IN User A's exclusion list.

2.  **Filtering Potential Matches based on *Their* Preferences matching User A:** This is the reciprocity check. The query needs to also check the `preferences` field *of the potential match* against User A's attributes:
    *   `preferences.interested_in_gender` includes "man" (User A's gender).
    *   `preferences.min_age` <= 30 (User A's age).
    *   `preferences.max_age` >= 30 (User A's age).

This entire set of conditions can be combined using Elasticsearch's boolean query (`bool` query with `filter` clauses). All clauses must match.

```json
// Simplified Elasticsearch Query DSL fragment
{
  "query": {
    "bool": {
      "filter": [
        // User A's preferences applied to potential match
        { "geo_distance": { "distance": "50km", "location": user_A_location } },
        { "range": { "age": { "gte": user_A_pref_min_age, "lte": user_A_pref_max_age } } },
        { "terms": { "gender": user_A_pref_genders } },

        // Potential match's preferences applied to User A (Reciprocity)
        { "terms": { "preferences.interested_in_gender": [user_A_gender] } },
        { "range": { "preferences.min_age": { "lte": user_A_age } } },
        { "range": { "preferences.max_age": { "gte": user_A_age } } },

        // Exclusions
        { "bool": { "must_not": { "terms": { "user_id": user_A_exclusion_list } } } }
      ]
    }
  },
  "sort": [ { "_geo_distance": { "location": user_A_location, "order": "asc", "unit": "km" } } ],
  "size": 20 // Or slightly more for buffer
}

```
By structuring the query this way, Elasticsearch performs the reciprocal check efficiently at search time, ensuring the returned `user_id`s satisfy both sides' basic criteria before the Matching Service even gets the results.

**Jacob (Interviewer):** Excellent. That clarifies how to integrate reciprocity directly into the search query. Now, you mentioned the data in Elasticsearch is eventually consistent, relying on an asynchronous indexing pipeline with a target lag of 1-2 minutes. What are the practical implications if, say, User B updates their location, but User A queries for matches *before* that update is indexed? User A might see User B at their old location, potentially outside the desired distance, or miss seeing them if they just moved closer. How big of a concern is this, and can we mitigate it?

**Alex (Candidate):** That's a valid consequence of choosing eventual consistency for better query performance.

*   **Impact:** The impact is usually minor and acceptable for dating apps.
    *   *Seeing stale location:* If User B appears at an old, slightly incorrect location, it's often not a deal-breaker. Distance is usually one factor among many.
    *   *Missing a newly relevant user:* If User B just moved into User A's range and isn't visible yet, it's a missed opportunity, but likely temporary. They'll probably appear in the next query session once the index catches up.
    *   *Preference Mismatch:* Similarly, if User B changes their age preference, they might incorrectly appear/disappear from User A's results for a brief period.
*   **Why it's Often Acceptable:** Users understand that location and profiles aren't always millisecond-accurate. The user experience degradation from a 1-2 minute delay is generally low compared to the performance hit of trying to achieve read-your-writes consistency across a distributed search index and multiple source databases for every match request. The latency requirement (<1s) would be very hard to meet otherwise.
*   **Mitigation/Management:**
    1.  **Monitoring Pipeline Lag:** The most crucial mitigation is closely monitoring the CDC pipeline lag. If lag consistently exceeds the target (1-2 mins), we need to investigate and scale the pipeline (CDC readers, Kafka brokers/partitions, indexing workers). This ensures the inconsistency window stays small.
    2.  **Setting User Expectations:** The UI might subtly indicate freshness ("updated recently") rather than implying real-time data.
    3.  **Triggering Refreshes:** When a user *actively* updates their own location or preferences in the app, the app *could* potentially trigger an immediate refresh of their matches after a short delay (e.g., 10-15 seconds), increasing the *chance* of seeing updated results sooner, though not guaranteeing it.
    4.  **Prioritizing Indexing:** We could potentially prioritize updates for actively searching users in the indexing pipeline, though this adds complexity.

Essentially, we accept minor, short-lived inconsistency as a trade-off for scalability and performance. The key is ensuring the "short-lived" part remains true through robust monitoring and pipeline management.

**Jacob (Interviewer):** That trade-off makes sense. Shifting slightly towards operations – what key metrics would you monitor for this Matching Service and its dependencies (like Elasticsearch) to ensure it's healthy and meeting its SLOs (Service Level Objectives), especially regarding latency and availability?

**Alex (Candidate):** Monitoring is critical here. I'd focus on several layers:

1.  **Matching Service API Metrics (Edge):**
    *   `Request Rate`: Overall load (requests/sec).
    *   `Error Rate`: Percentage of 5xx errors (indicating service failures) and potentially 4xx errors (indicating client issues or bad requests).
    *   `Latency (p50, p90, p99, p999)`: End-to-end time from receiving the request to sending the response. Track against the <1s p99 SLO. Break down latency contributions if possible (e.g., time spent waiting for Elasticsearch, time spent fetching profiles).
2.  **Elasticsearch Cluster Metrics:**
    *   `Query Latency (p50, p90, p99)`: Latency specifically for the search queries executed by the Matching Service. High latency here directly impacts API latency.
    *   `Query Rate`: Number of search queries per second hitting the cluster.
    *   `Indexing Latency`: Time taken to index documents.
    *   `Indexing Rate`: Documents indexed per second.
    *   `Index Refresh/Flush Latency`: How long index refreshes are taking.
    *   `Cluster Health Status`: Green (all shards available), Yellow (replicas unavailable), Red (primary shards unavailable - critical).
    *   `Resource Utilization`: CPU, Memory (especially JVM Heap usage), Disk I/O, Network I/O on data nodes. High utilization can predict performance degradation.
    *   `Rejected Tasks`: Number of search or indexing requests rejected due to overloaded queues (indicates insufficient capacity).
    *   `Pending Tasks`: Queue buildup for search or indexing.
3.  **Data Pipeline Metrics:**
    *   `End-to-End Lag`: Time from DB commit to document being searchable in Elasticsearch. Monitor against the 1-2 minute target. Track lag at different stages (CDC lag, Kafka lag, indexing worker processing time).
    *   `Throughput`: Messages/sec processed by the pipeline.
    *   `Error Rate`: Failures in capturing, transforming, or indexing data. DLQ size.
4.  **Dependency Service Metrics (User Profile, Location, Interactions):**
    *   Monitor their Availability and Latency (p99) as perceived by the Matching Service. Failures or slowness here will impact the Matching Service's ability to construct queries or enrich results.

**Alerting:** Set up alerts based on SLO violations (e.g., p99 latency > 1s for sustained period, error rate > X%), critical resource saturation (CPU/Heap > Y%), Elasticsearch cluster status going Yellow or Red, pipeline lag exceeding threshold, and high dependency error rates.

This comprehensive monitoring would give us visibility into the system's health, performance bottlenecks, and data freshness, allowing us to react proactively.

**Jacob (Interviewer):** Very thorough approach to monitoring. You've covered the key indicators across the different components involved.

Okay Alex, that really deepens the picture of the matching service. Thanks for exploring those details with me.

**(End Roleplay Continuation - Round 2)**

---



**Roleplay Continuation 2: Round 2 - User Matching Service**

**Jacob (Interviewer):** Alex, we've established a solid baseline design. Now, let's think about the future. Products evolve. How would you design this Matching Service, specifically the interaction with Elasticsearch, to accommodate **new matching criteria**? For example, suppose the product team wants to add filtering based on 'Verified Profile' status, or matching based on 'Shared Interests' extracted from user profiles. How would you integrate these without major redesigns?

**Alex (Candidate):** That's a common scenario. The choice of Elasticsearch actually helps here due to its schema flexibility, but we need a structured approach.

1.  **Data Indexing:** The first step is ensuring the new data points ('is_verified', list of 'interests') are consistently indexed into the Elasticsearch user documents. This involves:
    *   Updating the source-of-truth databases (e.g., User Profile DB) to store this information.
    *   Modifying the CDC pipeline (or other data propagation mechanism) to capture these new fields.
    *   Updating the indexing workers to include these fields in the JSON documents sent to Elasticsearch. Elasticsearch can often handle new fields dynamically (`dynamic: true` mapping), or we might update the index mapping explicitly beforehand if we want specific analysis or types.
2.  **Query Construction:** The Matching Service needs to be updated to potentially include these new filters in its queries to Elasticsearch.
    *   The API contract (`GET /v1/matches`) might not need to change initially if these are default filters or implicitly derived (e.g., maybe we *boost* verified profiles in sorting rather than strictly filtering).
    *   If they are user-selectable preferences (e.g., "Only show verified profiles" or "Prioritize shared interests"), the API *would* need extension (new query parameters).
    *   The core logic within the Matching Service that builds the Elasticsearch query DSL would need modification. It would add new `filter` clauses (e.g., `{"term": {"is_verified": true}}`) or potentially more complex logic for interest matching (e.g., using `terms` lookup for overlap, or perhaps adjusting the `sort` criteria based on the number of shared interests).
3.  **Decoupling:** It's important that the Matching Service doesn't become a monolith containing complex logic for every possible filter.
    *   Keep the Matching Service focused on orchestrating the query based on input parameters and core rules (like reciprocity, distance).
    *   The 'heavy lifting' of filtering/searching remains delegated to Elasticsearch.
    *   If matching logic becomes extremely complex (e.g., requiring machine-learned compatibility scores), that might warrant a separate *Scoring Service*. This service could pre-calculate scores, index them into Elasticsearch, and the Matching Service could then use these scores for sorting or filtering. Or, the Matching Service could retrieve candidates from ES and call the Scoring Service for re-ranking, though this adds latency.

Essentially, the approach is: get the data into the search index, update the query generation logic to use it, and maintain modularity by leveraging the search engine's capabilities and potentially introducing specialized services for highly complex calculations. The asynchronous indexing allows us to add new data points without downtime for the core matching query path.

**Jacob (Interviewer):** Okay, that covers adding new criteria. Now, how about **experimentation**? Let's say the product team wants to A/B test a new matching algorithm – perhaps one that prioritizes 'recently active' users higher, versus the current distance-based sorting. How could you implement A/B testing within this system design, ensuring we can reliably measure the impact of the change (e.g., on user engagement, match rates) without deploying risky changes to everyone?

**Alex (Candidate):** A/B testing matching logic is critical but needs care.

1.  **Experimentation Framework:** We'd need an overarching Experimentation Framework or Service. This service would be responsible for bucketing users into different experiment groups (e.g., Control group 'A' gets the old logic, Treatment group 'B' gets the new logic). The bucketing could be based on `user_id` hashmod or other stable criteria.
2.  **Integrating with Matching Service:** When a request comes into the Matching Service for a user:
    *   The Matching Service queries the Experimentation Framework (or uses a library integrated with it) to determine which experiment group (if any) the user belongs to for 'matching_algorithm' experiments.
    *   Based on the assigned group, the Matching Service adjusts its logic accordingly.
3.  **Implementing the Variation:**
    *   **Option A (Query Modification):** If the change involves modifying the Elasticsearch query (e.g., changing the `sort` clause, adding a `boost` for recent activity), the Matching Service constructs the appropriate ES query based on the user's group. This is relatively clean if the variations aren't drastically different.
        ```java
        // Pseudocode in Matching Service
        String userGroup = experimentationClient.getGroup(userId, "matching_algorithm_v2");
        QueryBuilder esQuery = buildBaseQuery(userProfile, location, exclusions); // Builds common filters

        if ("treatment_recently_active".equals(userGroup)) {
             esQuery.addSort(SortBuilders.fieldSort("last_active").order(SortOrder.DESC));
        } else {
             // Default/Control group sorting (e.g., distance)
             esQuery.addSort(SortBuilders.geoDistanceSort("location", userProfile.location).order(SortOrder.ASC));
        }

        List<Match> results = elasticsearchClient.search(esQuery);
        ```
    *   **Option B (Separate Endpoints/Service Versions - Less Ideal):** Deploying slightly different versions of the Matching Service and using the API Gateway or Load Balancer to route traffic based on experiment assignment. This adds deployment complexity and isn't great for small logic changes.
    *   **Option C (Feature Flags):** Use feature flags (potentially managed by the Experimentation Framework) within the Matching Service code to toggle specific logic blocks related to sorting or filtering. This is often combined with Option A.
4.  **Measuring Impact:**
    *   The Matching Service (or the downstream service that consumes the matches) needs to log events indicating which user saw which set of matches, and crucially, which experiment group they were in.
    *   These logs feed into an analytics pipeline where we can correlate experiment groups with key outcome metrics (e.g., swipe right rate, match rate, conversation rate, retention).
    *   This allows us to determine if the 'recently active' sorting (Group B) performs better than the distance sorting (Group A).

I'd favor **Option A/C (Query Modification controlled by Feature Flags/Experimentation Framework)** as it keeps the core infrastructure stable and allows dynamic changes to the query logic based on user assignment. It requires careful code organization within the Matching Service but is flexible for iterative testing.

**Jacob (Interviewer):** That's a practical approach to integrating A/B testing for algorithmic changes. Good thinking on leveraging an external framework and modifying the query logic dynamically.

---
**(End Roleplay Continuation 2)**

---

**Roleplay: Round 3 - Push Notification System**

**(Scene: Virtual Interview Room, third session)**

**Jacob (Interviewer):** Hi Alex, welcome to the third and final round. This session will focus more on operational aspects, reliability, asynchronous systems, and how systems evolve. The task is to design a **Push Notification System** for our dating app. This system needs to deliver various notifications – new match alerts, new message alerts, maybe some promotional content – to users' devices even when they aren't actively using the app. How would you approach this?

**Alex (Candidate):** Hi Jacob. A Push Notification system – definitely critical for engagement. As before, let's start with requirements.

*   **Functional Requirements:**
    *   What types of notifications need to be sent? (e.g., New Match, New Message, Profile Like, Promotional Campaign, Reminder/Re-engagement).
    *   What triggers these notifications? (e.g., events from other backend services like Matching or Messaging, scheduled jobs for campaigns).
    *   Do we need support for different notification formats? (e.g., simple text, badges, deep links into the app).
    *   User control: How do users manage their notification preferences (opt-out of certain types, or all)?
*   **Non-Functional Requirements:**
    *   **Delivery Guarantees:** What level of reliability is needed? At-least-once delivery is typically standard for push. Is exactly-once necessary (usually very hard for push)? What if a user's device is offline?
    *   **Latency:** How quickly should notifications be delivered after the triggering event? For messages/matches, near real-time (<10-15 seconds?) is desirable. For promotional content, latency might be less critical.
    *   **Scale:** How many notifications per day/second (peak)? Let's assume our 50M users might receive an average of, say, 5 transactional notifications (match/message) per day, plus occasional campaign pushes. Peak load during popular hours or for campaigns could be much higher – maybe thousands per second?
    *   **Throughput:** System needs to handle potentially large bursts, especially for campaigns targeting many users.
    *   **Availability:** The system needs to be highly available to trigger and attempt delivery.

**Jacob (Interviewer):** Good points. Let's clarify:
*   **Functional:**
    *   Types: New Match, New Message, App Reminders (e.g., "You have pending matches!"). Promotional campaigns are also needed.
    *   Triggers: Event-driven (from Match, Message services) and scheduled (for reminders, campaigns).
    *   Formats: Need basic text, ability to set badge count, and include payload for deep-linking.
    *   User Control: Yes, users must be able to opt-out per category (Match, Message, Promotion). The system must respect these preferences.
*   **Non-Functional:**
    *   **Delivery:** Aim for **at-least-once delivery**. We accept that if a device is offline for an extended period, the push platform (APNS/FCM) might drop the notification. We need to handle transient failures and retries robustly. Exactly-once is not required.
    *   **Latency:** Transactional (Match/Message) target p90 < 15 seconds from event to hitting APNS/FCM. Campaign latency less critical (minutes okay).
    *   **Scale:** Baseline average maybe 100-200 notifications/sec. Peaks for campaigns could reach **10,000 notifications/sec**.
    *   **Throughput:** Must handle bursts of 10k/sec.
    *   **Availability:** High availability (99.9%).

How would you architect a system to meet these requirements, particularly focusing on the asynchronous nature and reliability?

**Alex (Candidate):** Okay, the high peak throughput (10k/sec) and the need for different latency characteristics strongly suggest an asynchronous, queue-based architecture. Trying to handle this synchronously from triggering services would be unreliable and create backpressure.

**High-Level Architecture:**

1.  **API Gateway/Internal API:** An entry point for other backend services (Match, Message) or internal tools (Campaign Scheduler) to request a notification to be sent.
2.  **Notification Service (API Layer):** Receives requests. Performs initial validation, checks user notification preferences (critical!), potentially enriches the request (e.g., fetching device tokens), and then publishes a fully formed notification job onto a message queue. It does *not* interact directly with APNS/FCM.
3.  **Message Queue:** The core decoupling mechanism. Handles buffering requests, smoothing out bursts, and enables reliable processing. Needs to handle 10k msg/sec peak throughput.
4.  **Notification Dispatch Workers:** A pool of workers that consume messages from the queue. Each worker is responsible for:
    *   Taking a notification job off the queue.
    *   Formatting the payload specific to the platform (APNS for iOS, FCM for Android).
    *   Making the actual API call to APNS/FCM.
    *   Handling responses (success, failure, token invalidation).
    *   Updating status/logs.
5.  **Device Registration Store:** A database (perhaps NoSQL or a specialized cache) storing the mapping between `user_id` and their registered device tokens (APNS token, FCM registration ID), along with platform type (iOS/Android) and potentially app version. Needs fast lookups by `user_id`.
6.  **User Preferences Store:** A database/service holding user opt-in/opt-out settings for different notification categories. Needs fast lookups by `user_id`.
7.  **External Push Gateways:** Apple Push Notification Service (APNS) and Firebase Cloud Messaging (FCM) – the actual providers that deliver to devices.

**Workflow Example (New Match Notification):**

1.  Match Service detects a new match between User A and User B.
2.  Match Service calls the Notification Service API: `POST /v1/notifications` with payload like `{ "user_id": "userB", "type": "NEW_MATCH", "data": { "matched_user_id": "userA", "match_id": "..." } }`.
3.  Notification Service API receives the request:
    *   Looks up User B's notification preferences. If opted-out of Match notifications, stop processing (return success or specific code).
    *   Looks up User B's active device tokens from the Device Registration Store. If none, stop.
    *   Constructs the notification message payload (text, deep link data, badge update).
    *   Publishes one or more messages (one per device token/platform) to the Message Queue (e.g., topic `notification_jobs`). Message includes: `device_token`, `platform`, `payload`, `notification_id`.
    *   Returns `202 Accepted` to Match Service quickly.
4.  A Notification Dispatch Worker picks up a message for User B's iOS device.
5.  Worker formats the payload according to APNS requirements.
6.  Worker makes an HTTP/2 request to APNS.
7.  Worker processes the APNS response:
    *   Success: Log success, acknowledge message to the queue.
    *   Failure (e.g., temporary APNS error, rate limiting): Implement retry logic (don't ack message immediately, rely on queue's visibility timeout/redelivery).
    *   Failure (e.g., Invalid Token): Log failure, acknowledge message, *and* potentially trigger a cleanup process to remove the invalid token from the Device Registration Store.
    *   Failure (e.g., Device Unregistered): Same as invalid token.

**Jacob (Interviewer):** That's a clear, decoupled architecture. You mentioned the message queue needs to handle 10k msg/sec peak. Which message queue technology would you consider and why? How would you handle potential backpressure if APNS/FCM start responding slowly or rate limiting your workers?

**Alex (Candidate):** Choosing the right queue is crucial for that scale and throughput.

**Queue Technology Options:**

1.  **Apache Kafka:**
    *   *Pros:* Extremely high throughput, excellent scalability (partitioning), good persistence guarantees, supports consumer groups for scaling workers. Well-suited for high-volume event streams.
    *   *Cons:* Can have slightly higher operational complexity compared to managed services. Consumer offset management needs care. Latency might be slightly higher than pure message brokers for individual messages, but throughput is king here.
2.  **Managed Services (AWS SQS, Google Pub/Sub):**
    *   *Pros:* Excellent scalability, reduced operational overhead (serverless options), built-in features like Dead Letter Queues (DLQs), good integration with cloud ecosystems. SQS FIFO queues offer ordering if needed (though likely not per-user for notifications).
    *   *Cons:* Might have cost implications at massive scale. Potential provider-specific limits or throttling to understand.
3.  **RabbitMQ:**
    *   *Pros:* Flexible routing options, mature, supports various protocols (AMQP), good management UI.
    *   *Cons:* Clustering and achieving Kafka-level throughput can be more complex to manage yourself. Might struggle more at the 10k/sec persistent peak without careful tuning and hardware.

**Recommendation:** For a sustained peak of 10k messages/sec and potentially larger bursts during campaigns, **Apache Kafka** seems like the strongest technical fit due to its proven horizontal scalability and high throughput capabilities via partitioning. If we prefer reduced operational burden and are operating within a specific cloud provider, **AWS SQS (Standard Queues)** or **Google Pub/Sub** would also be strong contenders, as they are designed for massive scale with less management effort, and likely sufficient for this load. Let's proceed assuming **Kafka** for its raw throughput potential, but acknowledge a managed service is a very viable alternative.

**Handling Backpressure:**

Backpressure happens when consumers (Dispatch Workers) can't process messages as fast as they arrive, often because the downstream systems (APNS/FCM) are slow or rate-limiting.

1.  **Consumer Lag Monitoring:** Monitor Kafka consumer group lag. If lag increases significantly, it indicates backpressure.
2.  **Worker Auto-Scaling:** Configure the Notification Dispatch Worker pool to auto-scale based on queue length (consumer lag) or CPU/memory utilization. If APNS/FCM are healthy, adding more workers increases processing throughput.
3.  **Rate Limiting in Workers:** Workers should implement client-side rate limiting before calling APNS/FCM, respecting known limits or dynamically adjusting based on error responses (e.g., HTTP 429 Too Many Requests). Use algorithms like token bucket or leaky bucket.
4.  **Intelligent Retries:** When APNS/FCM respond with transient errors or rate limit codes (like 429), workers should *not* immediately retry. They should implement exponential backoff with jitter before retrying the same message. Importantly, they shouldn't acknowledge the message to Kafka during this backoff period, relying on Kafka's message redelivery after a visibility timeout. This prevents head-of-line blocking for other messages destined for different (potentially healthy) platforms or users.
5.  **Circuit Breaking:** If a worker detects a high sustained failure rate to APNS or FCM (e.g., >X% errors over Y minutes), it could implement a circuit breaker. It stops attempting calls to that specific provider for a short period, preventing hammering a potentially degraded service and failing faster.
6.  **Queue Buffering:** Kafka itself acts as a massive buffer. As long as the backpressure is temporary, Kafka can absorb the backlog (assuming sufficient disk space and retention configuration). The system catches up once the downstream issue resolves or workers scale up.

The key is to slow down the workers gracefully using retries/backoff/rate-limiting when downstream systems push back, let the queue absorb the temporary backlog, and scale workers appropriately.

**Jacob (Interviewer):** Excellent breakdown of queue choices and backpressure handling. Let's talk reliability. You mentioned at-least-once delivery and handling invalid tokens. If a worker fails *after* successfully sending to APNS/FCM but *before* acknowledging the message to Kafka, the message will be redelivered. How do you prevent sending a duplicate notification in that scenario? Also, how would you manage the cleanup of invalid device tokens efficiently?

**Alex (Candidate):** Those are key failure scenarios.

**Preventing Duplicates (Mitigation):**

Achieving true exactly-once delivery end-to-end (including the external APNS/FCM part) is extremely difficult. However, we aim for at-least-once and try to minimize duplicates caused by retries.

1.  **Idempotent Design (Ideal but Hard Externally):** If APNS/FCM supported idempotency keys for push requests (like some payment APIs do), we could generate a unique ID (`notification_id` from our Notification Service API) and send it with the push request. If the same ID is retried, APNS/FCM would recognize it and not resend. *Unfortunately, APNS/FCM generally don't offer this robustly for standard pushes.*
2.  **Logging & Auditing:** We log every attempt with the unique `notification_id`. If redelivery occurs, logs will show multiple attempts for the same ID. This doesn't prevent duplicates but helps diagnose issues.
3.  **Acceptance of Occasional Duplicates:** Given the limitations of external push providers, the most pragmatic approach is often to ensure the *internal* system handles retries correctly (via queue acknowledgements) and accept that rare duplicates might occur due to edge cases like worker crashes after successful sends but before ack. The impact of an occasional duplicate match/message notification is usually low annoyance, compared to the complexity of building a perfect exactly-once system involving external dependencies. We focus on making this window (send success to ack) as small as possible.
4.  **Application-Level Deduplication (Complex):** The receiving mobile app *could* potentially try to deduplicate notifications based on an included ID if multiple arrive close together, but this is unreliable (app might not be running).

So, primary strategy: rely on the message queue's at-least-once delivery, minimize the critical window between external call success and queue acknowledgement, and accept rare duplicates as a trade-off.

**Managing Invalid Tokens:**

When APNS/FCM response indicates a token is invalid or the device is unregistered (`Unregistered` for APNS, `NotRegistered` for FCM):

1.  **Worker Action:** The dispatch worker consuming the message should parse this specific error response.
2.  **Marking for Deletion:** Instead of immediately deleting the token (which might be risky if the error parsing is wrong), the worker should publish an event to another internal topic/queue (e.g., `invalid_token_detected`) with the `user_id` and the specific `device_token`.
3.  **Token Cleanup Service/Worker:** A separate, dedicated service or worker pool consumes from the `invalid_token_detected` queue.
4.  **Verification (Optional but Recommended):** This service could optionally add a small delay or check for frequency (e.g., if the same token is flagged multiple times within a short period) before acting.
5.  **Deletion:** The Cleanup Service then issues a command to the Device Registration Store to delete that specific token associated with the `user_id`.
6.  **Error Handling:** Handle failures in the deletion process (e.g., DB unavailable) with retries and potentially a DLQ for the cleanup events themselves.

This asynchronous cleanup decouples token management from the critical notification dispatch path, makes the system more resilient (a cleanup failure doesn't stop notifications), and allows for adding verification logic if needed.

**Jacob (Interviewer):** That pragmatic approach to duplicates and the asynchronous cleanup process for tokens make sense. Shifting to operations: what are the absolute key metrics you would monitor for this system's health and performance, and what kind of alerting would you set up?

**Alex (Candidate):** Monitoring is crucial for a system like this. Key metrics include:

1.  **End-to-End Notification Latency:** (Requires instrumentation across services) Time from initial API request to Notification Service -> Message hitting Queue -> Worker picking up -> Worker getting success/final failure from APNS/FCM. Track p50, p90, p99 for different notification types (transactional vs. campaign). Alert if transactional p90 > 15s.
2.  **Queue Depth / Consumer Lag:** For the main `notification_jobs` Kafka topic/partitions. A continuously growing lag indicates systemic backpressure or insufficient workers. Alert on sustained high lag.
3.  **Worker Throughput:** Notifications processed per second per worker / across the fleet.
4.  **Worker Error Rates:**
    *   Overall processing error rate (messages ending up in DLQ or failing permanently). Alert on high rates.
    *   Specific APNS/FCM Error Rates: Track rates of specific errors (Invalid Token, Rate Limited, Service Unavailable, Payload Error etc.). Spikes in specific errors indicate different problems (e.g., bad deployment, APNS/FCM issue, token DB corruption). Alert on unusual spikes.
5.  **Invalid Token Rate:** Rate at which tokens are flagged as invalid. A sudden surge could indicate an issue with token registration or a bad batch of tokens.
6.  **Notification Service API Metrics:** Request rate, error rate (5xx/4xx), latency (p99). Standard API monitoring.
7.  **Resource Utilization:** CPU/Memory/Network for Notification Service API instances, Dispatch Workers, Kafka Brokers, Device Store DB, Cleanup Service. Alert on saturation.
8.  **External Provider Health:** Although harder to monitor directly, track error rates returned by APNS/FCM closely as a proxy for their health.

Alerting Strategy: Focus on user-impacting issues (high latency, high overall error rate, critical queue lag) and system health indicators (resource saturation, dependency failures). Differentiate alert severity (e.g., P1 for major outage/latency spike, P2 for growing lag, P3 for specific error rate increases).

**Jacob (Interviewer):** Very comprehensive monitoring plan. Okay Alex, this covers the core design, reliability, and operational considerations very well. We've discussed the architecture, queueing, failure modes, and monitoring.

**(End Roleplay Round 3)**


---

**Roleplay Continuation: Round 3 - Push Notification System**

**Jacob (Interviewer):** Alex, your design handles the core asynchronous flow well. Let's focus on the **Promotional Campaigns**. You mentioned peaks of 10k notifications/sec, potentially driven by campaigns targeting millions of users. How would you design the system to handle sending a campaign to, say, 5 million users, within a reasonable timeframe (e.g., over an hour or two), *without* negatively impacting the latency of critical transactional notifications (New Match, New Message) that might be happening concurrently?

**Alex (Candidate):** That's a classic challenge – balancing bulk workload with low-latency traffic. We need mechanisms to isolate or prioritize traffic.

1.  **Separate Queues/Topics:** The simplest approach is to use separate Kafka topics (or SQS queues) for different priorities.
    *   `transactional_notifications` (High Priority): Used for New Match, New Message, etc. Dispatch workers for this topic could be configured for low latency, perhaps with fewer messages batched per poll, potentially more resource allocation.
    *   `campaign_notifications` (Lower Priority): Used for promotional blasts, reminders. Workers consuming from this topic can be optimized for throughput (larger batches, potentially fewer workers per message volume if latency isn't critical) and perhaps scaled up only during active campaigns.
    *   *Benefit:* Ensures a large campaign dump into the `campaign_notifications` topic doesn't delay messages in the `transactional_notifications` topic. Workers can focus on one type.
2.  **Priority Queuing (If supported/implemented):** Some message brokers offer message priority features, but Kafka doesn't natively support strict priority queuing easily across partitions in the way needed here. Relying on separate topics is usually more robust and scalable in Kafka's model. If using RabbitMQ, message priorities could be an option, but separate queues often remain simpler to manage operationally.
3.  **Worker Pool Allocation:** Even with separate topics, we need to manage the worker resources. We could have dedicated worker pools for each priority level, or a shared pool with logic to prioritize consuming from the high-priority topic. Dedicated pools offer better isolation. Ensure the high-priority pool always has sufficient capacity and isn't starved by resources allocated to campaign workers.
4.  **Rate Limiting the Campaign Ingestion:** When a campaign targeting 5M users is initiated, we shouldn't just dump 5M messages into the queue instantly. The Campaign Management tool or the initial API request handler should throttle the *publication* of messages into the `campaign_notifications` topic. For example, publish messages at a steady rate of, say, 1000-5000 messages/sec, allowing the campaign to spread out over the desired timeframe (e.g., 5M users at 1400 msg/sec takes about an hour). This smooths the load on the queue system and the dispatch workers from the start.
5.  **Downstream Throttling Awareness:** The campaign dispatch workers still need to respect APNS/FCM rate limits, potentially more aggressively than transactional workers if they are sending large volumes.

**Recommendation:** I would strongly recommend **Separate Kafka Topics** (`transactional_notifications`, `campaign_notifications`) with **Dedicated Worker Pools** for each, combined with **Rate-Limited Ingestion** of campaign messages at the source. This provides the best isolation and predictable performance for critical transactional notifications.

**Jacob (Interviewer):** Using separate topics and rate-limited ingestion makes sense for managing campaigns. Now, let's talk about **measuring effectiveness**. Sending a notification is only half the battle. How would you instrument this system and potentially collaborate with the mobile client to track metrics like:
*   Delivery success rates (reaching the device vs. just successfully sending to APNS/FCM).
*   Notification open rates (user tapping on the notification).
*   Downstream impact (e.g., did opening a 'New Match' notification lead to a conversation?).

**Alex (Candidate):** Measuring the full funnel is crucial for understanding ROI and optimizing notifications. This requires coordination between the backend and the mobile client.

1.  **Backend Delivery Tracking:**
    *   **Sent to Provider:** Our backend dispatch workers already log success/failure when sending to APNS/FCM. We aggregate this to get the "Sent Success Rate" (`successfully_sent_to_provider / total_attempts`). This is our baseline backend reliability metric. We also track the *reasons* for failures (Invalid Token, Provider Error, etc.).
    *   **Unique Notification ID:** Every notification request should have a unique ID generated by the Notification Service API (`notification_id`). This ID must be included in the payload sent to the device.
2.  **Client-Side Received Tracking (Delivery Confirmation):**
    *   APNS/FCM provide basic delivery reporting mechanisms, but they can be complex to implement reliably for individual message status and don't always guarantee confirmation of actual delivery to the device OS.
    *   A more common pattern is **client-side reporting**: When the mobile app's notification service extension (iOS) or service (Android) *receives* a push notification payload (even if the app is in the background), it extracts the `notification_id`.
    *   The app then makes a lightweight background API call back to our backend (e.g., `POST /v1/notifications/{notification_id}/received`) to confirm receipt.
    *   *Challenges:* This isn't 100% reliable (network issues, background execution limits), increases backend load, and adds client complexity. It gives a *better* signal than just "sent to provider" but isn't perfect delivery confirmation. We'd track "Client Reported Received Rate".
3.  **Client-Side Open Tracking:**
    *   When a user taps on the notification, the mobile app's handler code is invoked.
    *   At this point, the app extracts the `notification_id` and potentially campaign IDs or other metadata from the payload.
    *   The app sends an event back to our backend/analytics system (e.g., `POST /v1/notifications/{notification_id}/opened` or logs an analytics event like `notification_opened` with associated metadata).
    *   This allows us to calculate the "Open Rate" (`opens / reported_received` or `opens / sent_to_provider`).
4.  **Downstream Impact Tracking:**
    *   The notification payload should contain deep-linking information and context (e.g., `match_id`, `campaign_id`, `message_thread_id`).
    *   When the user opens the notification and interacts within the app (e.g., sends a message on that match thread, makes a purchase from the campaign), the app's standard analytics events should include the `notification_id` or `campaign_id` as attribution metadata.
    *   Our analytics pipeline (e.g., data warehouse processing event logs) can then join `notification_opened` events with subsequent user actions using this attribution ID to measure conversion rates and downstream impact.

**System Integration:**

*   Requires a robust **Analytics Pipeline** to collect and process events from both backend services (sent, failed) and mobile clients (received, opened, subsequent actions).
*   Needs clear API contracts between backend and client for reporting received/opened events.
*   Requires careful consideration of data volume and PII when logging/tracking.

This closed-loop tracking gives us visibility from initial send all the way to user action, allowing us to optimize notification content, timing, and targeting.

**Jacob (Interviewer):** That's a solid framework for tracking the full notification lifecycle and impact. It correctly identifies the need for client-backend collaboration and an analytics backend.

---
**(End Roleplay Continuation - Round 3)**



