## moja global Versioning

Most moja global tools are platforms combined with modules. This results in many dependencies which in turn require tight version control without losing flexibility. 

moja global projects use a slightly adapted [semantic versioning]() because projects need to be based on a science design. More eleborate discussion can be found [in the on-line discussion of SemVer](https://github.com/semver/semver/issues/323).  

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

### Pre-release Versioning  

TO BE COMPLETED  

Proposal: Use the typical alfa (a1, a2,..., a23), beta (b1, b2,..., b23), release candidate (c1, c2,..., c23) 

### Build Versioning  

TO BE COMPLETED

Proposal: Use the typical SNAPSHOT with a date (1.0.3-SNAPSHOT20190412) The developer or scientist needs to judge whether to increase the MAJOR, MINOR, or PATCH for the SNAPSHOT version. But the maintainers will revise this number when the proposed changes are submitted as a pull-request.  



