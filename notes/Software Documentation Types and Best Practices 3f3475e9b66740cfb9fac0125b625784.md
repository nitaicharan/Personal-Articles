# Software Documentation Types and Best Practices

Created: April 24, 2022 5:49 PM
Finished: No
Source: https://www.altexsoft.com/blog/business/technical-documentation-in-software-development-types-best-practices-and-tools/

![Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0KtOjp0GHyFdf3fcp.jpg](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0KtOjp0GHyFdf3fcp.jpg)

Documentation in software engineering is the umbrella term that encompasses all written documents and materials dealing with a software product’s development and use. All software development products, whether created by a small team or a large corporation, require some related documentation. And different types of documents are created through the whole [software development lifecycle](https://www.altexsoft.com/whitepapers/estimating-software-engineering-effort-project-and-product-development-approach/#utm_source=MediumCom&utm_medium=referral) (SDLC). Documentation exists to explain product functionality, unify project-related information, and allow for discussing all significant questions arising between stakeholders and developers.

![Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0vexuhMZX9gDoRvgY.png](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0vexuhMZX9gDoRvgY.png)

On top of that, documentation errors can set gaps between the visions of stakeholders and engineers and, as a result, a proposed solution won’t meet stakeholders expectations. Consequently, managers should pay a lot of attention to documentation quality.

# Agile and waterfall approaches

The documentation types that the team produces and its scope depend on the software development approach that was chosen. There are two main ones: agile and waterfall. Each is unique in terms of accompanying documentation.

[Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0N_wRnNWMP6hMDZgr](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0N_wRnNWMP6hMDZgr)

**Waterfall** is a linear method with distinct goals for each development phase. Teams that use waterfall spend a reasonable amount of time on product planning in the early stages of the project. They create an extensive overview of the main goals and objectives and plan what the working process will look like. Waterfall teams strive to create detailed documentation before any of the engineering stages begin. Careful planning works well for projects with little to no changes in progress as it allows for precise budgeting and time estimates. However, waterfall planning has proven to be ineffective for long-term development as it doesn’t account for possible changes and contingencies on the go. According to [PMI’s 9th Global Project Management Survey,](https://www.pmi.org/-/media/pmi/documents/public/pdf/learning/thought-leadership/pulse/pulse-of-the-profession-2017.pdf) the Agile approach is used by 71 percent of organizations for their projects.

![Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0uZ3JWAyImD-7Svq5.png](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0uZ3JWAyImD-7Svq5.png)

The [agile approach](https://www.altexsoft.com/whitepapers/agile-project-management-best-practices-and-methodologies/#utm_source=MediumCom&utm_medium=referral) is based on teamwork, close collaboration with customers and stakeholders, flexibility, and ability to quickly respond to changes. The basic building blocks of agile development are iterations; each one of them includes planning, analysis, design, development, and testing. The agile method doesn’t require comprehensive documentation at the beginning. Managers don’t need to plan much in advance because things can change as the project evolves. This allows for just-in-time planning. As one of the Agile Manifesto values suggests, putting — “working software over comprehensive documentation -“, the idea is to produce documentation with information that is essential to move forward, when it makes the most sense.

Today, agile is the most common practice in software development, so we’ll focus on documentation practices related to this method.

# Types of documentation

The main **goal** of effective documentation is to ensure that developers and stakeholders are headed in the same direction to accomplish the objectives of the project. To achieve them, plenty of documentation types exist.

Adhering to the following classifications.

![Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/08QwLPTH2NHdfJSsr.png](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/08QwLPTH2NHdfJSsr.png)

All software documentation can be divided into **two main categories:**

- **Product documentation**
- **Process documentation**

**Product documentation** describes the product that is being developed and provides instructions on how to perform various tasks with it. Product documentation can be broken down into:

- System documentation and
- User documentation

**System documentation** represents documents that describe the system itself and its parts. It includes requirements documents, design decisions, architecture descriptions, program source code, and help guides.

**User documentation** covers manuals that are mainly prepared for end-users of the product and system administrators. User documentation includes tutorials, user guides, troubleshooting manuals, installation, and reference manuals.

**Process documentation** represents all documents produced during development and maintenance that describe… well, process. The common examples of process documentation are project plans, test schedules, reports, standards, meeting notes, or even business correspondence.

The main difference between process and product documentation is that the first one record the process of development and the second one describes the product that is being developed.

# Product: System documentation

System documentation provides an overview of the system and helps engineers and stakeholders understand the underlying technology. It usually consists of the requirements document, architecture design, source code, validation docs, verification and testing info, and a maintenance or help guide. It’s worth emphasizing that this list isn’t exhaustive. So, let’s have a look at the details of the main types.

# Requirements document

A requirements document provides information about the system functionality. Generally, requirements are the statements of what a system should do. It contains business rules, user stories, use cases, etc. This document should be clear and shouldn’t be an extensive and solid wall of text. It should contain enough to outline the product’s purpose, its features, functionalities, and behavior.

The best practice is to write a requirement document using a single, consistent template that all team members adhere to. The one web-page form will help you keep the document concise and save the time spent on accessing the information. Here’s a look at an example of a one-web-page product-requirements document to understand various elements that should be included in your PRD. Nevertheless, you should remember that this isn’t the one and only way to compile this document.

![Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0LZGw5v0-0tlsiKxR.png](Software%20Documentation%20Types%20and%20Best%20Practices%203f3475e9b66740cfb9fac0125b625784/0LZGw5v0-0tlsiKxR.png)

*One web-page product-requirements document created by using [Atlassian Confluence](https://www.atlassian.com/software/confluence), the content collaboration software*

Here are the main recommendations to follow:

1. **Roles and responsibilities**. Start your document with the information about project participants including a product owner, team members, and stakeholders. These details will clarify responsibilities and communicate the target release goals for each of the team members.
2. **Team goals and a business objective**. Define the most important goals in a short point form.
3. **Background and strategic fit**. Provide a brief explanation about the strategic aim of your actions. Why are you building the product? How do your actions affect the product development and align with company’s goals?
4. **Assumptions.** Create a list of technical or business assumptions that the team might have.
5. **User Stories.** List or link user stories that are required for the project. A user story is a document written from the point of view of a person using your software product. The user story is a short description of customer actions and results they want to achieve.
6. **User interaction and design**. Link the design explorations and wireframes to the page.
7. **Questions.** As the team solves the problems along the project progression, they inevitably have many questions arising. A good practice is to record all these questions and track them.
8. **Not doing.** List the things which you aren’t doing now but plan on doing soon. Such a list will help you organize your teamwork and prioritize features.

Make all this information more comprehensive by using the following practices:

- Use **links and anchors**. They will help you make the document easier to read and search as readers will be able to comprehend the information gradually. For instance, you can provide links to customer interviews and anchors to previous discussions or other external information related to the project.
- Use **diagramming tools** to better communicate the problems to your team. People are more likely to perceive information by looking at the images than reading an extensive document. Different visual models will help you to perform this task and outline requirements more effectively. You can incorporate diagrams into your requirements process using the following software diagramming tools: Visio, Gliffy, Balsamiq, Axure or SmartArt in Microsoft Office.

# Software architecture document

Software architecture design documents include the main architectural decisions. We don’t recommend listing everything, but rather focus on the most relevant and challenging ones. An effective design and architecture document comprises the following information sections:

**Design document template.** Discuss and form a consensus with stakeholders regarding what needs to be covered in the architecture design document before it has been created and use a defined template to map architectural solutions.

**Architecture & Design Principles**. Underline the guiding architecture and design principles with which you will engineer the product. For instance, if you plan to structure your solution using [microservices architecture](https://www.altexsoft.com/blog/engineering/using-microservices-for-legacy-system-modernization/#utm_source=MediumCom&utm_medium=referral), don’t forget to specifically mention this.

**User Story description.** Connect user stories with associated business processes and related scenarios. You should try to avoid technical details in this section.

**Solution details.** Describe the contemplated solution by listing planned services, modules, components, and their importance.

**Diagrammatic representation of the solution.** Identify the diagrams that need to be created to help understand and communicate the structure and design principles.

# Source code document

A source code document is a technical section that explains how the code works. While it’s not necessary, the aspects that have the greatest potential to confuse should be covered. The main users of the source code documents are software engineers.

Source code documents may include but are not limited to the following details:

- HTML generation framework and other frameworks applied
- Type of data binding
- Design pattern with examples (e.g. model-view-controller)
- Security measures
- Other patterns and principles

Try to keep the document simple by making short sections for each element and supporting them with brief descriptions.

# Quality assurance documentation

There are different types of testing documents in agile. We have outlined the most common:

- Test strategy
- Test plan
- Test case specifications
- Test checklists

A **test strategy** is a document that describes the software testing approach to achieve testing objectives. This document includes information about team structure and resource needs along with what should be prioritized during testing. A test strategy is usually static as the strategy is defined for the entire development scope.

A **test plan** usually consists of one or two pages and describes what should be tested at a given moment. This document should contain:

- The list of features to be tested
- Timeframes
- Roles and responsibilities (e.g. unit tests may be performed either by the QA team or by engineers)

A **test case specifications** document is a set of detailed actions to verify each feature or functionality of a product. Usually, a QA team writes a separate specifications document for each product unit. Test case specifications are based on the approach outlined in the test plan. A good practice is to simplify specifications description and avoid test case repetitions.

**Test checklist** is a list of tests that should be run at a particular time. It represents what tests are completed and how many have failed. All points in the test checklists should be defined correctly. Try to group test points in the checklists. This approach will help you keep track of them during your work and not lose any. If it helps testers to check the app correctly, you can add comments to your points on the list.

# Maintenance and help guide

This document should describe known problems with the system and their solutions. It also should represent the dependencies between different parts of the system.

# Product: User documentation

As the name suggests, user documentation is created for product users. However, their categories may also differ. So, you should structure user documentation according to the different user tasks and different levels of their experience. Generally, user documentation is aimed at two large categories:

- end-users
- system administrators

The documentation created for **end-users** should explain in the shortest way possible how the software can help solve their problems. Some parts of user documentation, such as tutorials and onboarding, in many large customer-based products are replaced with onboarding training. Nevertheless, there are still complex systems remaining that require documented user guides.

The online form of user documentation requires technical writers to be more imaginative. Online end-user documentation should include the following sections:

- FAQs
- Video tutorials
- Embedded assistance
- Support Portals

In order to provide the best service for end-users, you should collect your customer feedback continuously. The wiki system is one of the more useful practices. It helps to maintain the existing documentation. If you use the wiki system you won’t need to export documents to presentable formats and upload them the servers. You can create your wiki pages using a wiki markup language and HTML code.

**System administrators’** documents don’t need to provide information about how to operate the software. Usually, administration docs cover installation and updates that help a system administrator with product maintenance. Here are standard system administrators documents:

- **Functional description** — describes the functionalities of the product. Most parts of this document are produced after consultation with a user or an owner.
- **System admin guide** — explains different types of behaviors of the system in different environments and with other systems. It also should provide instructions how to deal with malfunction situations.

# Process Documentation

Process documentation covers all activities surrounding the product development. The value of keeping process documentation is to make development more organized and well-planned. This branch of documentation requires some planning and paperwork both before the project starts and during the development. Here are common types of process documentation:

**Plans, estimates, and schedules.** These documents are usually created before the project starts and can be altered as the product evolves.

**Reports and metrics.** Reports reflect how time and human resources were used during development. They can be generated on a daily, weekly, or monthly basis. Consult our article on [agile delivery metrics](https://www.altexsoft.com/blog/business/agile-software-development-metrics-and-kpis-that-help-optimize-product-delivery/) to learn more about process documents such as velocity chats, sprint burndown charts, and release burndown charts.

**Working papers.** These documents exist to record engineers’ ideas and thoughts during project implementation. Working papers usually contain some information about an engineer’s code, sketches, and ideas on how to solve technical issues. While they shouldn’t be the major source of information, keeping track of them allows for retrieving highly specific project details if needed.

**Standards.** The section on standards should include all coding and UX standards that the team adheres to along the project’s progression.

The majority of process documents are specific to the particular moment or phase of the process. As a result, these documents quickly become outdated and obsolete. But they still should be kept as part of development because they may become useful in implementing similar tasks or maintenance in the future. Also, process documentation helps making the whole development more transparent and easier to manage.

The main goal of process documentation is to reduce the amount of system documentation. In order to achieve this, write the minimal documentation plan. List the key contacts, release dates, and your expectations with assumptions.

# General practices for all types of documents

There are several common practices that should be applied to all the types of documents we discussed above:

# Write just enough documentation

You should find a balance between no documentation and excessive documentation. Poor documentation causes many errors and reduces efficiency in every phase of a software product development. At the same time, there is no need to provide an abundance of documentation and to repeat information in several papers. Only the most necessary and relevant information should be documented. Finding the right balance also entails analyzing the project’s complexity before development starts.

# Documentation is an ongoing process

This means that you should keep your documentation up-to-date. It is very important as documents that aren’t current automatically lose their value. If requirements change during software development, you need to ensure that there’s a systematic documentation update process that includes information that has changed. You can use automatic version control to manage this process more efficiently.

# Documentation is the collaborative effort of all team members

The agile method is based on a collaborative approach to creating documentation. If you want to achieve efficiency, interview programmers and testers about the functionalities of the software. Then, after you have written some documentation, share it with your team and get feedback. To get more information try to comment, ask questions, and encourage others to share their thoughts and ideas. Every team member can make a valuable contribution to documents you produce.

# Hire a tech writer

If you can, it will be the worth hiring an employee who will take care of your documentation. The person who generally does this job is called a technical writer. A tech writer with an engineering background can gather information from developers without requiring someone to explain in detail what is going on. It’s also worth embedding a technical writer as team member, locating this person in the same office to establish close cooperation. He or she will be able to take part in regular meetings and discussions.

# Final word

The agile methodology encourages engineering teams to always focus on delivering value to their customers. This key principle must also be considered in the process of producing software documentation. Good software documentation should be provided whether it is a specifications document for programmers and testers or software manuals for end users. Comprehensive software documentation is specific, concise, and relevant.

As we have mentioned above, it’s not obligatory to produce the entire set of documents described in this article. You should rather focus only on those documents that directly help achieve project objectives.