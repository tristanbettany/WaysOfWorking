# Ways Of Working

These ideas exists to detail the most ideal ways of working in a small non-agile development team and may or may not
align to your ideas. Some ideas have the potential to be based soley on personal opinion so if you think that is the 
case please choose to ignore it. Take from these ideas what you believe would work for you or your organisation.

>NOTE: This document assumes that somebody is looking to impliment ways of working and processes into their development
team or project and do not already have one in place.

Although some of the rules below may be also fully or partially covered by standards in PHP-FIG, it is important to 
re-iterate and explain some things as the importance of some of these rules can often be overlooked, and the rule 
ignored.

## Coding Standards

Teams should ideally all be on the same page and adhear to an agreed upon list of standards that benefit both the 
development team and the business. These rules should not just blindly follow PSR rules however also integrate 
styles that the development team like to adopt.

### PSR-12 Should always be followed where possible

It is not always possible to follow PSR rules, esspecially when dealing with frameworks and extending their classes.
For that reason you should be required to adhear to PSR-12 in all cases unless there is a specific reason it cannot 
be done.

https://www.php-fig.org/psr/psr-12/

### Comment only what the code cannot say

As of PHP 7.4 Comments and docblocks have no need in existing unless used for parsing in the same way Doctrine does.
With type hinting for all paramaters on methods and classes including return type hinting most information we need to
know is in the code. The only expection I have come across for this is `@throws`. This rule insinuates that you name
variables well

### Scope should be restrictive where possible

Sometimes it is not always possible to choose the scope of methods or variables if you are extending a class from a 
3rd party or framework, however in most cases you can define the scope. When defining scope all class variables should 
be private and have getters and setters added. Any mehods not intended to be used outside of the class should be 
private, unless the intent is for the class to be extended and those methods to be used from the child.

### Whitespace & dead code

There should always be 1 blank line at the bottom of every file. Not all IDEs may enforce this so make sure to check 
your IDE is configured correctly. You can also set this up in `.gitattributes`. Within methods there should not be any 
stray linebreaks that do not increase readability. For example line breaks should exist to seperate sections of control 
logic. 

There should not be any methods that exist with an empty body, or a body with just a comment.
Any code that is no longer needed should not be commented out and commited, it should be deleted and git will maintain 
that history.

### Input should always be validated

All user input to the application should be validated. Not just for validity but also for silly mistakes that the user 
might make such as leaving white space on the end of a dicount code (this should be trimmed).

### Commit only working code

The git tree should be traversable back in time without the codebase running into fatal exceptions due to incomplete 
features. When commiting code, think about how you break up the commits, sometimes commiting file by file or even chuck
by chunk to create a git tree that flows nicley. Should you need to commit broken code to allow a fellow developer to
help debug with you, tag the commit with `WIP:` and once solved, swiftly commit again the solution to that issue alone.

### Automate proceedures with CLI commands

When developing a new feature it is a good idea to add certain aspects of that feature as CLI commands. Sometimes you 
may recieve requests from the business to perform these tasks once the application is live, and you'll pleased
you made those commands then.

