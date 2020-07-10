## moja global Versioning

Most moja global tools are platforms combined with modules. This results in many dependencies which in turn require tight version control without losing flexibility.

The Standard Format for the version number consists of 3 indicators (MAJOR.MINOR.PATCH) and is only assigned at the moment the version is released. For work in progress a label is used as a placeholder.

### Standard Format

The indicators in the version number will be changed as follows:

MAJOR is increased when you:
* Make backwards incompatible changes
* Change the science design so a restructuring of the code or recoding the whole module is required

MINOR is increased when you:
* Make backward-compatible changes
* Change the science design so code changes within the existing framework are required

PATCH is increased when you:
* You make backwards-compatible bug fixes
* Change the science design so no code changes are necessary

### Build Versioning

* Feature branches and the develop branch should always build snapshot versions including date (e.g. 2.6.0-SNAPSHOT201205012).
* Release branches and hotfix branches should always build release candidate versions (e.g. 2.6.0-0.rc1).
* The master branch should always build unqualified versions - versions without build numbers (e.g. 2.6.0).

The developer or scientist needs to judge whether to increase the MAJOR, MINOR, or PATCH for the SNAPSHOT version. But the maintainers will revise this number when the proposed changes are submitted as a pull-request.



