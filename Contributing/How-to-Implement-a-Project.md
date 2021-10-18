## Project Implementation

This process assumes that you already have [started a new project](How-to-Start-a-New-Project.md) and have an approved [**Science Design**](How-to-Agree-on-a-Science-Design.md)

1.  Confirm Your Team:

    -   Contact the maintainers and documentation writers of the repository you want to code for and discuss your project. Agree upon the `write` and maybe the `admin` permissions. Assign additional maintainers and documentation writers as needed. 
    -   Assign a Coach who is willing to introduce new developers to the project. 
    -   Publicise widely that the project is ready to be developed and is looking for engineers, documentation writers, testers and more. When moja global users collaborate everybody wins. Contact [tsc@moja.global](mailto:tsc@moja.global) to get support with reaching out to the community of users.
    -   Emphasize to all new contributors to review the [Coding Guidelines](../Governance/CODING-GUIDELINES.md) and the [Contribution Criteria](../CONTRIBUTING.md).

2.  User Stories and Minimum Viable Product:

    -   Use User stories to translate a **Science Design** into a task list that can be engineered and developed. 
    -   When the scientific design is approved, organise a conference call with all the science contributors (at a minimum the maintainers of the **Science Design** have to be present) and the interested developers (at a minimum the maintainers of the future code should be present) to contribute to the project. Real-time interaction is important so it would be better to have fewer participants on a call rather than more participants through a written document.
    -   The developers can ask for clarifications from the scientists to ensure that every aspect of the **Science Design** is clear. 
    -   Next developers and scientists individually draft all the user stories they can think of, into the GitHub Project Board. All user stories will have the following syntax: `As a [User, Admin, …], I can [action: click, scroll, …] to [result: calculate, open, load, erase, …]`.
    -   After a first drafting session, the User Stories are classified as `essential` or `later`. All the User Stories classified as essential must be part of the Minimum Viable Product (MVP) which means that none of them can be dropped without breaking the `why` of the project. Check every user story several times to check whether it can be dropped from the MVP.
    -   All the User Stories are moved to the **To do** column of the GitHub Project Board. The MVP stories are moved to **In progress**.

3.  Agile Architecture:

    -   Agree on an initial project architecture with the software development team. This improves communication, increases the speed and quality of the coding while reducing risks.
    -   There are no fixed rules about what you should define up front. Your minimum outcome is that everybody agrees on how the project should be built and how it should fit into the wider software components. 
    -   It is logical to define the interactions with the existing system and sketch a technology diagram linking the user interface with the business process and with the data. 
    -   Use [branding](../Governance/BRANDING.md) and [User Interface Style](https://github.com/moja-global/.github/blob/master/Governance/User-Interface-Style.md) of the wider software components.

4.  Iterative Build:

    -   Build the MVP in the initial sprint. 
    -   Document the code in the code base and in the Wiki. Everybody can edit the Wiki. If you wish to create documentation that needs a maintainer approval, use regular Markdown files.
    -   Develop tests, in collaboration with Scientists, to check whether the software is working accurately. The code is tested continuously based on these tests. The tests are upgraded continuously with the code. 
    -   Invite feedback from users after every sprint. The feedback is used to improve documentation in the Wiki and to update the backlog. 
    -   Take the feedback into account when you prioritize the backlog and start a new sprint. 
    -   Continue this cycle until the project is ready for a first release. 

5.  Approve Release:
    -   Once the code is ready for release, document the tests that have been performed, copy the Wiki to the repository and create a [release-candidate version](How-to-Assign-a-Version.md) that includes the Wiki and all other documentation. 
    -   Submit the pre-release to the scientists and code maintainers for a final review.
    -   Invite the independent scientists to participate in the review if a science review panel was used. Document test results and feedback from the reviewers and maintainers.
    -   Complete all the tests and bug-fixes.
    -   Submit the pre-release with a pull-request, and indicate that you want [@TSC to review your pull-request](https://help.github.com/en/articles/requesting-a-pull-request-review).
    -   The project will be released with the agreed version number.
