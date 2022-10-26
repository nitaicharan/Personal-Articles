# Marcin Wanago Blog

Created: May 26, 2022 11:25 PM
Finished: No
Source: https://wanago.io/
Tags: #tool

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/mikroorm.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/mikroorm.png)

So far, in this series, we’ve used a few different solutions for managing databases. To work with MongoDB, we’ve used Mongoose. To manage a PostgreSQL database, we’ve utilized TypeORM and Prisma. This article looks into another Object-relational mapping (ORM) library called MikroORM. By having a broad perspective on what’s available, we can choose the library […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/react_hook_form_recursive-1.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/react_hook_form_recursive-1.png)

React Hook Form is a popular library that helps us deal with forms and keep their code consistent across the whole application. In this article, we look into how to allow the user to shape the form to some extent and create data structures that are recursive. In the end, we get the following form: […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/recursive_formik.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/recursive_formik.png)

Formik is a popular tool that helps us write React forms and keep their code consistent across even big projects. Besides having a fixed number of properties, we sometimes need to allow the user to shape the form to some extent and create recursive data structures. In this article, we learn how to do that […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/file_inputs_cypress.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/file_inputs_cypress.png)

Our applications sometimes have features that allow users to choose files from their machine. Also, we might allow the users to download various files. Testing that with Cypress might not seem straightforward at first. In this article, we build a simple React application that allows the users to choose a JSON file from the hard […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/resetting_mocks-1.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/resetting_mocks-1.png)

Jest offers a lot of functionalities for mocking functions and spying on them. Unfortunately, there are some pitfalls we need to watch out for. In this article, we explain how spying on functions works. We also explain how and why we should reset our mocks. Creating mock functions The most straightforward way of creating a […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/mocking.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/mocking.png)

In the fourth part of this series, we’ve learned the basics of mocking API calls. However, there are often situations where we would like to test various more demanding cases. So, in this article, we implement more advanced examples on how to mock with Jest. Creating a simple React component Let’s start by creating a […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/abortController.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/abortController.png)

When developing applications, data fetching is one of the most fundamental tasks. Despite that, there are some things to watch out for: one of them is race conditions. This article explains what they are and provides a solution using the AbortController. Identifying a race condition A “race condition” is when our application depends on a […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/enzyme_2.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/enzyme_2.png)

A few days ago, React released version 18, which is not compatible with Enzyme. Furthermore, it probably is not achievable to use Enzyme with React 18. If you’re still using Enzyme, it is time to look into alternatives. The most popular option besides Enzyme seems to be React Testing Library. However, incorporating RTL requires a […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/referer.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/referer.png)

As web developers, we should care about the privacy of our users. This article explains what the Referer header is and what information it exposes. We also learn to use the Referrer-Policy to control how many details the referer header should include. We can increase privacy and deal with some potential security issues by doing […]

![Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/clickjacking.png](Marcin%20Wanago%20Blog%204337696e97c54f4a9657b91ab7a523dd/clickjacking.png)

When developing an application, we need to ensure that our users are safe from various attacks. Unfortunately, the web has a lot of mechanisms that can be abused. In this article, we explore the idea of iframes and underline the danger of clickjacking. We also learn how to deal with this problem using the X-Frame-Options […]