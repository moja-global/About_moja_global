## moja global Versioning

Most moja global tools are platforms combined with modules. This results in many dependencies which requires tight version control without losing any flexibility. 

moja global projects use Vincent Driessen's [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html) and therefore moja global uses the [related versioning system](https://datasift.github.io/gitflow/Versioning.html). You can find a more elaborate discussion on the following [discussion of SemVer](https://github.com/semver/semver/issues/323).  

The Standard Format for the version number consists of 3 indicators: **MAJOR.MINOR.PATCH**. It is only assigned at the moment the version is released. For a work in progress, a label is used as a placeholder.  

### Standard Format

The indicators in the version number will be changed as follows:

1.  **MAJOR** is increased when you:

    -   Make backwards-incompatible changes.
    -   Change the **Science Design** so a restructuring of the code or re-development of the whole module is required.

2.  **MINOR** is increased when you:

    -   Make backwards-compatible changes.
    -   Change the **Science Design** so the code changes within the existing framework are required.

3.  **PATCH** is increased when you:
    -   You make backwards-compatible bug fixes
    -   Change the science design so no code changes are necessary

### Build Versioning

-   Feature branches and the develop branch should always build snapshot versions including the date. Example: `2.6.0-SNAPSHOT201205012`.
-   Release branches and hotfix branches should always build release candidate versions. Example: `2.6.0-0.rc1`.
-   The master branch should always build unqualified versions - versions without build numbers. Example: `2.6.0`.

The developer or scientist needs to judge whether to increase the **MAJOR**, **MINOR**, or **PATCH** for the **SNAPSHOT** version. The maintainers will revise this number when the proposed changes are submitted as a pull-request.
