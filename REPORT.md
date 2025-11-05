# Lab 4 WebSocket -- Project Report

## Description of Changes
In this laboratory I completed the ``onChat`` test in the ElizaServerTest file. To make the tests run 
successfully I also completed the ``onMessage`` function of the ``ComplexClient`` class.

## Technical Decisions
To complete both the test and the function I followed the steps described in the code.
First I started completing the ``onMessage`` function
```kotlin
fun onMessage(
        message: String,
        session: Session,
    ) {
    logger.info { "Client received: $message" }
    list.add(message)
    latch.countDown()
    // 5. COMPLETE if (expression) {
    // 6. COMPLETE   sentence
    // }
    if (list.size == 3) {
        session.basicRemote.sendTextSafe("i think")
    }
}
```
I had to check when the list of messages had 3 message, 
this indicates that Eliza has been connected with the client:
- The doctor is in.
- What's on your mind?
- '---'
So when the list had these 3 message the client should send 'i think'

The next step was to complete the test
```kotlin
@Test
fun onChat() {
    logger.info { "Test thread" }
    val latch = CountDownLatch(4)
    val list = mutableListOf<String>()

    val client = ComplexClient(list, latch)
    client.connect("ws://localhost:$port/eliza")
    latch.await()
    val size = list.size
    // 1. EXPLAIN WHY size = list.size IS NECESSARY -> condicion de carrera entre los 2 assert equals
    // 2. REPLACE BY assertXXX expression that checks an interval; assertEquals must not be used;
    assert(size == 4 || size == 5)
    // 3. EXPLAIN WHY assertEquals CANNOT BE USED AND WHY WE SHOULD CHECK THE INTERVAL
    // 4. COMPLETE assertEquals(XXX, list[XXX])
    assertEquals("Do you really think so?", list[3])
}
```
The ``value`` size is necessary to avoid race conditions between multiple assertions.
List can be updated asynchronously, so ``list.size`` can be changed during the test,
that is why I store the value, to prevent assertions with different values.
Then, ``assertEquals`` must not be used because the list size may vary slightly between different runs,
it depends on timing or thread scheduling.
The last assertion cheks if Eliza's response is the expected one.
The index of the expected message will always be 3 because there are three introduction messages, then one form the client
and the fourth message is the response I have to check.

## Learning Outcomes
I found the laboratory very interesting because I hadn’t realized 
that such a problem could directly affect a test. 
Also, I did not know about the existence of Eliza, so that was quite interesting to.

## AI Disclosure
### AI Tools Used
- Chatgpt

### AI-Assisted Work
AI was only used to correct some parts of this report, translate some phrases and
check verb tenses and grammatical errors.
The percentage of AI-assisted work will be 20% for AI 80% for me


### Original Work
I wrote the code in the ``ElizaserverTest`` file, and wrote most of the report.