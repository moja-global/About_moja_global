## Agree on a Science Design

moja global tools have to be in line with the latest science and international standards. Therefore all of the code implementations are informed by a **Science Design** stored in the Science sub-directory of each repository. Code revisions might require a revision of the **Science Design**. When in doubt, ask the Coaches or the Maintainers or just follow the below steps:

1.  Invite your Team and start a new Project:
    -   While starting a new project and inviting a team to join, make sure that you invite (at least 1) accomplished scientists to review or develop a **Science Design**.
    -   If according to your scientist, your project does not require changes to the existing Science Design, write a short justification mentioning the [version](How-to-Assign-a-Version.md) of your feature or the project in the title.
    -   Submit a pull-request to the appropriate sub-directory with the existing **Science Design**. Request [@TSC to review your pull-request](https://help.github.com/en/articles/requesting-a-pull-request-review) and share feedback.  
    -   If according to your scientist, your project requires a (revision of the) Science Design, proceed with the next step.
2.  Create a [Google Doc](http://docs.new/) for the **Science Design**:
    -   Make a copy of the existing **Science Design** and change the [version](How-to-Assign-a-Version.md) in the title.  
    -   If no **Science Design** exists, copy [the standard header](https://docs.google.com/document/d/1feo9G91bbjth9RZ4606Rag4tAdRxuYpfnlWecs-gbbY/edit?usp=sharing) into a new Google Doc, along with the **Title** and **Abstract** from the GitHub repository and add the [version](How-to-Assign-a-Version.md) to the title.
    -   Fetch a shareable link for the Google Doc with permissions to edit. Reference it back on GitHub in a file with the same name and version in the Science sub-directory of the Project. This would allow the visitors to easily find the Google Doc.
3.  Appoint Maintainers and Editors:
    -   Every document should have ideally 2 maintainers and/or editors.
    -   The Maintainers focus on the substance of the document. The Editors focus on the readability of the document.
    -   Two editors and two maintainers are ideal so they can review each other’s suggestions. Having 2 persons with combined maintainer/editor functions can be sufficient for projects with a small science component.
    -   Add the names to the header of the Google Doc.
4.  Invite Collaborators for the Project:
    -   Reach out to all the collaborators that would be interested in contributing.
    -   Bigger and more diverse groups tend to come up with better designs. Different levels of expertise are also a bonus.  
    -   Set the Rule of Collaboration on text using the [standard list of rules](https://github.com/moja-global/.github/blob/master/Governance/Standard-Rules-for-Collab-on-Text.md) as a basis.
5.  Agree on the Report Structure:
    -   Develop a brain map: Ask the collaborators to add key topics that the **Science Design** needs to cover. Ask them to progressively group the topics into a logical structure. For more information please refer to page 2 of the [Standard Header Doc](https://docs.google.com/document/d/1feo9G91bbjth9RZ4606Rag4tAdRxuYpfnlWecs-gbbY/edit?usp=sharing).
    -   After a reasonable amount of time, ask all collaborators for their final suggestions and fix the report structure. Please note that this doesn't mean that the structure is final. Changes are always possible and welcomed. It is more important to have the first structure within a reasonable amount of time than to invest in the ideal structure for too long.
6.  Draft the **Science Design**:
    -   For each section of the report, a drafter is appointed. This drafter will produce the first version of the section by an agreed date. 
    -   All other contributors can comment or suggest.
    -   The editors will write the background, acknowledgements, etc. and they will edit the text already written. (Editing is not rewriting! Rewriting can confuse and discourage the drafter.)
    -   Invite all collaborators to provide edits and comments by a specific date. 
7.  Release the **Science Design**:
    -   After the final date has been agreed upon for the draft, the **Science Design** should be copied (with comments) into a Google Doc with the same title but with a [Release Candidate Version](How-to-Assign-a-Version.md).
    -   All the remaining comments are dealt with by the drafters and the maintainers. Additional comments are not allowed except if they point out errors or anti-patterns.
    -   The final draft is edited. 
8.  Review and Approval by the Science Panel:
    -   A Science Panel consists of at least 1 expert recognized as an authority in the field.
    -   The Science Panel reviews the **Science Design** Release Version. Maintainers make improvements and respond.
    -   When there are no further comments, the Science Panel provides a short opinion at the end of the **Science Design**.
    -   The final version including the Science Panel opinion is downloaded as a PDF with the same title but with a [Master Version](How-to-Assign-a-Version.md).
9.  Approval by the Technical Steering committee: 
    -   Upload a PDF version of the **Science Design** Master Version to the Science sub-directory of your repository via a pull-request. 
    -   Through the pull-request, indicate that you want [@TSC to review your pull-request](https://help.github.com/en/articles/requesting-a-pull-request-review).
    -   Once it has been approved, proceed with the [regular project implementation cycle](https://github.com/moja-global/.github/blob/master/Contributing/How-to-Implement-a-Project.md).