###MasterCodeReview



- **Part-1: Forge a better code review process** 
    - Why should my team perform code reviews?      
        - Mistakes      
        - Distributed Ownership     
        - Collaboration     
        - Coaching      
        - Accountability
    
    - Signs of a bad code review process        
        - Lazy reviews: Rarely any comments or "LGTM!"
        - Reviews are not kind/make you anxious: "This is totally not readable/maintainable"            
        - Not being aware of the context (Speed OR quality), for ex. nitpicks are bad/stalling if someone is quickly trying to put a fix out.
        - Reviews the code partially : This leads to heavy churn and have long discussion thread. like 2 comments in 1st review then again another few reviews.         
        - We have a large queue of minor bug reports.       
        - PR are blocked for minutae/nits (style).      
    
    - A good code review process    
        - Objective: The code should be production ready before the PR is merged. It should be Asynchronous.       
        - Author Expectations: 
            - Should be small: 
            - Should be scoped appropriately.   
                - Functional: Includes test.
                - Passes builds.
                - Should be deployable and safely rolled back.      
            - Refactoring:
                - Team should decide whether refactoring should included along with the original PR OR should be separated to a new PR.     
            - Include the WHAT and Why in the code review description.   
            - Should have extras if necessary: Establish the standard with the team
                - Related tickets/ PRs/ Issues.
                - Test coverage.
                - Screen captures   
                - Rollback safety
                - Backwards compatibility
            - Assign the right reviewer!    
            - Drive resolution to conflicts - **See it through to merge!**      
        - Reviewer Expectations:    
            - Exhibit ownership and responsibility.
            - Get to them fast.
            - Have high standards.
            - Favor pragmatism, **NOT** perfectionism.  
            - Be Kind.
            - Be thorough
            - Don't comment on style! Use linters.  
            - Justified Blocking:   
                - Too big
                - Flaws, testing gaps, broken builds
                - Risks
                - Over-engineering
                - Obvious readability gaps
            - Unjustified Blocking
                - Nitpicks(specially style) 
                - Emergencies
                - Utilize "comment and approve" when appropriate.   
        - Guidelines:
            - Document in Team Wiki - collaborate with your team!       
            - Good guidelines have:
                - When and how to open a PR.    
                - Size and scope of PRs. ex. DB migrations. 
                - Author and reviewer expectations
                - PR templates      
                - Escalation procedures 
                - When and when not to refactor.    
                
    - KPI - Forge a better code review process
        - Decreased number of defects.
        - Decreased number of review cycles.
        - Speed of new developer onboarding.
        - Discussion quality.   

- **Part-2: Give better code review**   
    - What to look for
        - Why:
            - Ownership and responsibility
            - Flaws first
            - Readability second    
            - Learning opportunities    
        - Before reading the diff    
            - Scope: Max LOC/ Include refactoring or not
            - Builds, checks and tests: Passing  
            - Conflicts: If the PR has conflicts     
            - Screenshots: Proof of change   
            - Did they solve the problem - the right problem?   
        - Flaws within the diff
            - Edge cases/corner cases
            - Testing: Junit handle all scenarios?
            - Business requirement gaps 
            - Unexpected behaviour changes  
            - Optimization: Opportunities for perf optimizations
            - Documentation:
            - Complexity / over engineering: Code should be simple
            - Premature optimizations: Lookout for such changes
        - Flaws outside the diff
            - Partial refactoring: Are all usages or references refactored.
            - Side effects: Check if any changes can bring side-effects   
            - Changes that aren't backwards compatible
            - Rollback risks 
        - Readability gaps
            - Intent should be obvious  
            - Naming: Should be relevant/concise/conveyWhatIsBeingDone
            - Abstraction:   
            - Directories: Should be grouped appropriately
            - Should follow conventions across the repo
            - Implementation and tests should be readable.    
    - How to perform code review
        - Why?
            - Avoid anlaysis paralysis
            - Provide a transparent thought process
            - Give good feedback: accurate and unambiguous
        - **Process: The how?**    
            - Get context: PR description/related issues/tickets
            - Determine if you're the appropriate reviewer
            - Set aside time    
            - Optimize for speed OR quality? Know the authors priority.
            - Start with the curx: Find the critical piece of implementation    
            - Read randomly : To increase understanding
                - Expand context lines(the ones not part of the diff)
                - Existing repository code
            - Go class by class after you understand.   
            - Have mindset that there are Flaws lurking that needs to be found out - You're a detective
            - Commenting
                - Write comments as you go - that is writing your thoughts down
                - once you piece through the puzzle some comments might be obsolete/need rewriting
                - Leave positive comments - But don't overdo it!
                - Make a clear approval decision    
            - Effective Comments:
                - Comment on the code, not the person. 
                - Propose a path forward OR collaborate.    
                - Use nit and nit-blocker
                - Give a reason why 
                - Provide background, add links
                - **BAD** 
                    - "You didn't check for a null value."
                    - "Change the variable name"
                    - “Query the table index, not a GSI.”
                    - “This solution is not optimal. The time complexity is O(n^2)”       
                - **GOOD**
                    - "This input value could be null, causing a server error. If null, a client error
                  should be thrown."
                    - "What would happen if the input value is null?"
                    - "Nit, non-blocker: I'd recommend changing the variable name from
                      'purchaseStatus' to 'orderStatus'. The variable stores the status of an order, not a
                      purchase."        
                    - “This queries a Global Secondary Index, which will be an eventually consistent
                      read. There are race condition situations where the record’s version could get
                      updated by another handler immediately before this query. This will fail optimistic
                      locking and throw an exception. This needs a strongly consistent read — the
                      DynamoDB table index should be queried here.”
                    - The time complexity here is O(n^2). If we sort the array first, we can achieve a
                      time complexity of O(n*log(n)). This will be processing a large data set in a
                      synchronous critical path, so it’s worth optimization.    
                          
                
                
- **Chapter-3:Forge a better code review process** 

- **Chapter-4:Forge a better code review process** 
