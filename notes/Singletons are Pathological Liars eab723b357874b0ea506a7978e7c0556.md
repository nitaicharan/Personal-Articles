# Singletons are Pathological Liars

Created: September 26, 2022 10:26 AM
Finished: No
Source: http://misko.hevery.com/2008/08/17/singletons-are-pathological-liars/

So you join a new project, which has an extensive mature code base. Your new lead asks you to implement a new feature, and, as a good developer, you start by writing a test. But since you are new to the project, you do a lot of exploratory “What happens if I execute this method” tests. You start by writing this:

```
testCreditCardCharge() {
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(100);
}
```

This code:

- Only works when you run as part of the suite.
- When run in isolation, throws NullPointerException.
- When you get your credit card bill, you are out $100 for every time the test runs.

Now, I want to focus on the last point. How in the world did the test cause an actual charge on my credit card? Charging a credit card is not easy. The test needs to talk to a third party credit card web-service. It needs to know the URL for the web-service. It needs to authenticate, pass the credentials, and identify who the merchant is. None of this information is present in the test. Worse yet, since I don’t even know where that information is present, how do I mock out the external dependencies so that every run does not result in $100 actually being charged? And as a new developer, how was I supposed to know that what I was about to do was going to result in me being $100 poorer? That is “Spooky action at a distance!”

But why do I get NullPointerException in isolation while the test works fine when run as part of the suite? And how do I fix it? Short of digging through lots of source code, you go and ask the more senior and wiser people on the project. After a lot of digging, you learn that you need to initialize the CreditCardProcessor.

```
testCreditCardCharge() {
  CreditCardProcessor.init();
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(100);
}
```

You run the test again; still no success, and you get a different exception. Again, you chat with the senior and wiser members of the project. Someone tells you that the CreditCardProcessor needs an OfflineQueue to run.

```
testCreditCardCharge() {
  OfflineQueue.init();
  CreditCardProcessor.init();
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(100);
}
```

Excited, you run the test again: nothing. Yet another exception. You go in search of answers and come back with the knowledge that the Database needs to be initialized in order for the Queue to store the data.

```
testCreditCardCharge() {
  Database.init();
  OfflineQueue.init();
  CreditCardProcessor.init();
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(100);
}
```

Finally, the test passes in isolation, and again you are out $100. (Chances are that the test will now fail in the suite, so you will have to surround your initialization logic with “if not initialized then initialize” code.)

The problem is that the APIs are pathological liars. The credit card pretends that you can just instantiate it and call the charge method. But secretly, it collaborates with the CreditCardProcessor. The CreditCardProcessor API says that it can be initialized in isolation, but in reality, it needs the OfflineQueue. The OflineQueue needs the database. To the developers who wrote this code, it is obvious that the CreditCard needs the CreditCardProcessor. They wrote the code that way. But to anyone new on the project, this is a total mystery, and it hinders the learning curve.

But there is more! When I see the code above, as far as I can tell, the three init statements and the credit card instantiation are independent. They can happen in any order. So when I am re-factoring code, it is likely that I will move and rearrange the order as a side-effect of cleaning up something else. I could easily end up with something like this:

```
testCreditCardCharge() {
  CreditCardProcessor.init();
  OfflineQueue.init();
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(100);
  Database.init();
}
```

The code just stopped working, but I had no way to knowing that ahead of time. Most developers would be able to guess that these statements are related in this simple example, but on a real project, the initialization code is usually spread over many classes, and you might very well initialize hundreds of objects. The exact order of initialization becomes a mystery.

How do we fix that? Easy! Have the API declare the dependency!

```
testCreditCardCharge() {
  Database db = Database();
  OfflineQueue q = OfflineQueue(db);
  CreditCardProcessor ccp = new CreditCardProcessor(q);
  CreditCard c =  new CreditCard(
    "1234 5678 9012 3456", 5, 2008);
  c.charge(ccp, 100);
}
```

Since the CreditCard charge method declares that it needs a CreditCardProcessor, I don’t have to go ask anyone about that. The code will simply not compile without it. I have a clear hint that I need to instantiate a CreditCardProcessor. When I try to instantiate the CreditCardProcessor, I am faced with supplying an OfflineQueue. In turn, when trying to instantiate the OfflineQueue, I need to create a Database. The order of instantiation is clear! Not only is it clear, but it is impossible to place the statements in the wrong order, as the code will not compile. Finally, explicit reference passing makes all of the objects subject to garbage collection at the end of the test; therefore, this test can not cause any other test to fail when run in the suite.

The best benefit is that now, you have seams where you can mock out the collaborators so that you don’t keep getting charged $100 each time you run the test. You even have choices. You can mock out CreditCardProcessor, or you can use a real CreditCardProcessor and mock out OfflineQueue, and so on.

Singletons are nothing more than global state. Global state makes it so your objects can secretly get hold of things which are not declared in their APIs, and, as a result, Singletons make your APIs into pathological liars.

Think of it another way. You can live in a society where everyone (every class) declares who their friends (collaborators) are. If I know that Joe knows Mary but neither Mary nor Joe knows Tim, then it is safe for me to assume that if I give some information to Joe he may give it to Mary, but under no circumstances will Tim get hold of it. Now, imagine that everyone (every class) declares some of their friends (collaborators), but other friends (collaborators which are singletons) are kept secret. Now you are left wondering how in the world did Tim got hold of the information you gave to Joe.

Here is the interesting part. If you are the person who built the relationships (code) originally, you know the true dependencies, but anyone who comes after you is baffled, since the friends which are declared are not the sole friends of objects, and information flows in some secret paths which are not clear to you. You live in a society full of liars.

[ Follow up: [Where Have All the Singletons Gone](http://misko.hevery.com/2008/08/21/where-have-all-the-singletons-gone/) ]

[ Follow up: [Root Cause of Singletons](http://misko.hevery.com/2008/08/25/root-cause-of-singletons/) ]