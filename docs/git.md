# git


## Tagging

To create a branch from a tag

    git checkout -b hotfix/4.0.1 v4.0.0

To add tags:

    git tag v4.0.1

Push the tags:

    git push --follow-tags

Tag after/later

    git tag -a v4.0.1 9fceb02

## Hashes

Where the hash belongs to the commit, i.e 

    git log --pretty=oneline
    15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
    a6b4c97498bd301d84096da251c98a07c7723e65 Create write support
    0d52aaab4479697da7686c15f77a3d64d9165190 One more thing
    6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
    0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc Add commit function
    4682c3261057305bdd616e23b64b0857d832627b Add todo file
    166ae0c4d3f420721acbb115cc33848dfcc2121a Create write support
    9fceb02d0ae598e95dc970b74767f19372d61af8 Update rakefile <--- tag this
    964f16d36dfccde844893cac5b347e7b3d44abbc Commit the todo
    8a5cbc430f1a9c3d00faaeffd07798508422908a Update readme