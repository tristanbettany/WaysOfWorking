# Ways Of Working

This is a collection of ideas and advice which exists to detail the most ideal and realistic ways of working 
in a small web development team and may or may not align to your ideas. The information should be relevant irrespective
of your chose workflow (agile/waterfall, etc). 

The purpose of this is purley because following publicly produced standards are a great idea on paper but in practise
they either dont work in their entirity or they dont cover anything other than code syntax.

Some ideas here have the potential to be based soley on personal opinion so if you think that is the case please choose to 
ignore it. Take from these ideas what you believe would work for you or your organisation.

Although some of the rules below may be also fully or partially covered by standards in PHP-FIG, it is important to 
re-iterate and explain some things as the importance of some of these rules can often be overlooked, and the rule 
ignored.

>NOTE: This document assumes that somebody is looking to impliment ways of working and processes into their development
team or project and do not already have one in place.

## Coding Standards

Teams should ideally all be on the same page and adhear to an agreed upon list of standards that benefit both the 
development team and the business. These rules should not just blindly follow PSR rules however also integrate 
styles that the development team like to adopt.

### PSR-12 Should always be followed where possible

It is not always possible to follow PSR rules, esspecially when dealing with frameworks and extending their classes.
For that reason you should be required to adhear to PSR-12 in all cases unless there is a specific reason it cannot 
be done, or there is a rule the team as a whole has chosen to ignore.

https://www.php-fig.org/psr/psr-12/

### Comment only what the code cannot say

Pre PHP 7.4 you really needed certain parts of your classes to have docblocks and comments, but as of PHP 7.4 
Comments and docblocks have no need in existing unless used for parsing in the same way Doctrine does.
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

### Use of a FQCN (Fully Qualified Class Name)

In modern PHP no class should ever be reffered to with a FQCN. All namespaces should go in the use statements at the
top of a class.

### Line Length should not exceed 120 chars

Lines should not exceed 120 chars long to avoid the need to scroll sideways. This can be a pain when people dont 
have a mouse that allows sideways scrolling. 

### Method Readability

A method should be able to be read predominantly by scanning down, and marginally left to right. Increasing scanning
from left to right decreases readability.

When method paramaters exceed 1 they should be seperated onto multiple lines to increase readability and decrease the 
possibility of sideways scrolling. For example:

```php
public function foo(int $bar, string $diddly, array $squat) :void
{
    //Some Logic
}
```

would become:

```php
public function foo(
    int $bar,
    string $diddly,
    array $squat
) :void {
    //Some Logic
}
```

In this case it is far easier to spot the method paramater types and to see if any paramaters are missing type hints.

### Naming Conventions

Methods and variables should always be named using camel case and not be ambiguous. When choosing a descriptive method 
name you must do so with care thinking of the future developer looking at your code. For example:

```php
getCat(); //This makes sense to you, to another developer theres more than one meaning
private string $cat; //This makes sense to you, to another developer theres more than one meaning
```

```php
getCategory(); //This is better and less ambiguous
private string $category; //This is better and less ambiguous
```

99% of the time most methods will be prefixed with either get, set, or is, based on its operation. Get should be used 
for methods that return data, set for methods that change data, and is for methods that tell you the status of existing
data. For example:

```php
getName();
setName();
isLive();
```

All boolean variables should be prefixed with is. For example:

```php
private bool $isActive;
private bool $isTest;
```

## Proceedures & Best Practises

### Boy Scouting

The boy scouting rule states that `you should always leave the campground cleaner than you found it`. This should be
adopted by all developers to help make sure that technical debt doesnt become a huge problem.

This means that all classes you make changes to when writing new features, should have a concious effort made to
cleanup any existing code and fix any technical debt within reason and scope of those classes. This includes changes
to the code to make it meet company standards not previously set and stamping out bugs.

All this ensures that whenever a new feature is worked on improvments to existing code always comes along with it and
technical tebt does not mount up.

### Commit only working code

The git tree should be traversable back in time without the codebase running into fatal exceptions due to incomplete 
features. When commiting code, think about how you break up the commits, sometimes commiting file by file or even chuck
by chunk to create a git tree that flows nicley. Should you need to commit broken code to allow a fellow developer to
help debug with you, tag the commit with `WIP:` and once solved, swiftly commit again the solution to that issue alone.

### Testing

All code created should have testing done using PHPUnit. When creating an API, you will need integration tests for each
endpoint, including unit tests for the individual sections of code. When consuming APIs you will need to either stub 
the API endpoints your using or get hold of a sandboxing environment for that API which you can use in local.

You should be hitting as many edge cases in testing as possible both happy and unhappy paths. Your tests should be
structured in that 1 feature or unit is tested per file with multiple scenarios in the file as seperate test methods.

For example it is not recommended to have and `OrderTest` file which has mutiple test scenarios that create, add, edit.
You should infact have a `CreateOrderTest` which has multiple scenarios for creating a test correctly and incorrectly.

### Automate proceedures with CLI commands

When developing a new feature it is a good idea to add certain aspects of that feature as CLI commands. Sometimes you 
may recieve requests from the business to perform these tasks once the application is live, and you'll be pleased
you made those commands then.

### Pair coding

Pair coding is not or everyone and doesnt work in all teams. It can sometimes require a strong bond and similar
working style between 2 developers. When this rare situation is found it should be encouraged for solving complex 
issues and debugging difficult mystical problems. THe power of 2 minds working together can be far better than 1 mind
going round and round in circles.

### Design and architechture

Use the right tool for the right job, do not use tools just because it is new and on trend as that can lead to
technical debt and/or security issues in the future. Make sure to use design patterns when they are needed to solve a 
problem, and don't use the business you work for as a testing ground for new un-proven design patterns that may not 
solve your problem.

## Pull Requests

### Submitting

### Commenting

### Merging

## Code of Conduct

Generally be good to one another, have time for your fellow developer and they will have time for you.