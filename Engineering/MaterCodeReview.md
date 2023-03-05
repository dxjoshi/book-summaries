###MasterCodeReview



- **Part-1:Forge a better code review process** 
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

- **Part-2:Give better code review** 
- **Chapter-3:Forge a better code review process** 

- **Chapter-4:Forge a better code review process** 
