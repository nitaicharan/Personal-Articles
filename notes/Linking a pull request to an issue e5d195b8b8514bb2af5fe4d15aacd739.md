# Linking a pull request to an issue

Created: May 10, 2022 9:03 PM
Finished: No
Source: https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue

You can link a pull request to an issue to show that a fix is in progress and to automatically close the issue when the pull request is merged.

**Note:** The special keywords in a pull request description are interpreted when the pull request targets the repository's *default* branch. However, if the PR's base is *any other branch*, then these keywords are ignored, no links are created and merging the PR has no effect on the issues. **If you want to link a pull request to an issue using a keyword, the PR must be on the default branch.**

You can link an issue to a pull request manually or using a supported keyword in the pull request description.

When you link a pull request to the issue the pull request addresses, collaborators can see that someone is working on the issue.

When you merge a linked pull request into the default branch of a repository, its linked issue is automatically closed. For more information about the default branch, see "[Changing the default branch](https://docs.github.com/en/github/administering-a-repository/changing-the-default-branch)."

You can link a pull request to an issue by using a supported keyword in the pull request's description or in a commit message (please note that the pull request must be on the default branch).

- close
- closes
- closed
- fix
- fixes
- fixed
- resolve
- resolves
- resolved

If you use a keyword to reference a pull request comment in another pull request, the pull requests will be linked. Merging the referencing pull request will also close the referenced pull request.

The syntax for closing keywords depends on whether the issue is in the same repository as the pull request.

[Untitled](Linking%20a%20pull%20request%20to%20an%20issue%20e5d195b8b8514bb2af5fe4d15aacd739/Untitled%20Database%2098be6c92c9094da7a549fc0a816c0a37.csv)

Only manually linked pull requests can be manually unlinked. To unlink an issue that you linked using a keyword, you must edit the pull request description to remove the keyword.

You can also use closing keywords in a commit message. The issue will be closed when you merge the commit into the default branch, but the pull request that contains the commit will not be listed as a linked pull request.

## Manually linking a pull request to an issue

Anyone with write permissions to a repository can manually link a pull request to an issue.

You can manually link up to ten issues to each pull request. The issue and pull request must be in the same repository.

1. 
    
    On GitHub.com, navigate to the main page of the repository.
    
2.  
    
    Under your repository name, click **Pull requests**.
    
    ![Linking%20a%20pull%20request%20to%20an%20issue%20e5d195b8b8514bb2af5fe4d15aacd739/repo-tabs-pull-requests.png](Linking%20a%20pull%20request%20to%20an%20issue%20e5d195b8b8514bb2af5fe4d15aacd739/repo-tabs-pull-requests.png)
    
3. 
    
    In the list of pull requests, click the pull request that you'd like to link to an issue.
    
4. 
    
    In the right sidebar, in the "Development" section click .
    
5. 
    
    Click the issue you want to link to the pull request.
    
    ![Linking%20a%20pull%20request%20to%20an%20issue%20e5d195b8b8514bb2af5fe4d15aacd739/link-issue-drop-down.png](Linking%20a%20pull%20request%20to%20an%20issue%20e5d195b8b8514bb2af5fe4d15aacd739/link-issue-drop-down.png)
    
- "[Autolinked references and URLs](https://docs.github.com/en/articles/autolinked-references-and-urls/#issues-and-pull-requests)"