# Ways Of Working

This is a collection of ideas and advice which exists to detail the most ideal and realistic ways of working 
in a small web development team and may or may not align to your ideas. The information should be relevant irrespective
of your chosen workflow (agile/waterfall, etc). 

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

### Be strict

Your control logic should always be written stric so as to avoid ambiguity and introduction of bugs. As a developer
you should always know exactly what your logic is comparing against and never should allow the code to almost guess.
By that I mean DONT do the following things:

```php
if ($val) {
    //do logic
}
```

Doing this allows your logic to run if the value is truthy which can be a boolean true or any string or any object or
any array. Pretty much most things that arent boolean false or null. The reason this is bad is because if something 
goes wrong elsewhere in the application and the `$val` gets set to something you dont expect your logic still runs
either making it seem like nothing is wrong or causing a fatal further down the line.

The same applies to the following

```php
if (!$val) {
    //do logic
}
```

Notice the difference is the exclimation mark. This reverses the logic to check for falsey values, so as said before
the same problems exist as above with the added problem of the exclimation mark is very! easy to miss when a developer 
is scanning it and can cause errors in additional logic added to the same class or control logic where the developer
didnt realise the logic was that way round.

The fix is to always! be strict with your checks, for example like this:

```php
if ($val === true) {
    //do logic
}
if ($val === 'a string') {
    //do logic
}
```

The advantage of doing this is when an error occurs you will be taken to the correct location when debugging and no doubt
will find it easier to work out how to fix this logic. It is also much more verbose as to what is going on in your control
logic.

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

Often you come across methods which the domain logic inside them doesnt point towards a getter or a setter. Instead
they point more towards something like `calculateFoo()` or `processFoo()`. These sitations often cannot be avoided,
however it's good to think about if the method `processPriceFromInput()` for instance could actually just be named
`getPrice()`.

All boolean variables should be prefixed with is. For example:

```php
private bool $isActive;
private bool $isTest;
```

## Proceedures & Best Practises

There are a number of proceedures and best practises which can help improve you code quality and how you develop as a
team, here are some of the best ive picked up over the years.

### Code SOLID

I wont explain solid here but the gist of it can generally be summed up by saying `be aware of seperation of concerns`,
read more about it here - https://en.wikipedia.org/wiki/SOLID

### Code DRY

Coding DRY means `Dont Reapeat Yourself`, which sometimes is harder said than done. This though can be accomplished
with the boy scouting practise which allows you to cleanup code after its done incrimentally. So here you should be
abstracting and DRYing up code either as you go along and/or part of doing Boyscouting.

### Throw Exceptions

Make sure that as you are writing code you are thinking about all possible edge cases that could happen which may not
be something that your code should handle and make sure to throw exceptions for it. By doing so these exceptions can be
caught at a later point and transformed into something more friendly for the user where if the application is an API for
example, this leaves the consumer of the API in a position where they are to handle it and avoid hitting that exception.
Effectivley pushing that problem onto them and not you.

The more exceptions you throw on edge cases can also help in debugging too.

### Catch & Log

Particularly when making external requests consuming APIs or running jobs, make sure you are dropping in logs if an
exception is thrown. Should the company you work for have a plan wth a log management service such as Rollbar you will
be able to easily see these errors and be alerted of problems. This will make debugging much much easier.

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

### Aiming for high Code Coverage by testing all edge cases

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

### Plan your design and architechture

A well planned architechture for an application is much better than unplanned. There is no shame in spending the 
first few days of developing a new feature or application, thinking through ideas, architechture and prototyping. 
Jumping straight into creating a new feature or application will inevitably have you coding yourself into a corner and 
you will need to spend time refactoring aspects as you move further into the feature/s. Planning it all out to start with
avoids that, even if this means getting a pen to paper and drawing some ERD's.

Use the right tool for the right job, do not use tools just because it is new and on trend as that can lead to
technical debt and/or security issues in the future. Make sure to use design patterns when they are needed to solve a 
problem, and don't use the business you work for as a testing ground for new un-proven design patterns that may not 
solve your problem.

## Git Flow

Git Flow should be used where acceptable to improve collaboration on projects in the team and make deployments
easier to manage.

More details on Git flow can be found here - https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

## Pull Requests

A development team generally leans heavily on pull requests to assure code quality and to make sure that work is done 
on time. Lets see the best way PR's can be managed to keep all the cogs moving.

### Submitting

When submitting a pull request the last thing a developer reviewing it wants is to come to review it and find that the 
branch is out of date with the target branch and the description is lacking details of what you have done.

Make sure that you are keeping your PRs up to date with the target branch, until they have been reviewed. To do this 
you can do the following:

```
git fetch
git checkout your-branch-in-for-pr
git merge --no-ff origin/target-branch
```

When it comes to a description, its all very well and good linking to a ticket that was the request for the new feature,
however this could be and generally will be a rather high level description of the request. It's your responsability to
describe the technical implimentation you chose and the reasons behind it. Maybe some limitations as to why you didnt choose
an obvious one and went with something more complex. You should also think about any special requirements for testing
and will the reviewer need to know this. Also for an audit trail its wise to include a screenshot for any visual changes
you made.

### Reviewing & Commenting

To make sure to keep on top of PR's it is advisable to reserve 30 mins at the end of each day to review them.
Each PR should be reviewed for coding standards, syntx issues, and of course domain/business logic. 

Commenting on PR's can be tricky, too often developers can comment on PRs with little thought or concern for the person 
recieving the comment. If the developer has spent alot of time working on it, some comments can feel quite grating and 
can be taken to heart. Due to this, always make sure to do your best to convey tone of voice in a PR comment using emojis,
and if you are trying to explain something particularly complex maybe even follow up the comment with a message on Teams.

Sometimes you may notice a mistake has been made in a PR which may have simply been an oversight and could potentially be
slightly embaresing to mention publicly in PR comments. This can sometimes then be best to contact the developer directly via
Teams to let them know some improvements they can make before you do an official review.

All of this helps avoid any confict and toxic situations in comments sections of PRs.

### Approval & Merging

This section of PRs should be fairly simple. It is recommended that when there are at least 2 (or the amount the business is happy with) 
approvals from reviewers which should come from a collection of developers picked by the company.

Senior developers should then be able to merge it once these requirements have been met. If an update to that PR is made after the approvals quota
is reached then the quota is reset and the approval process restarts before a merge can be completed.

You should always merge in a way that maintains the hillocks in the git tree, and remove the branch after merging.
You can do this like so:

```
git checkout target-branch
git merge --no-ff branch-to-merge
git branch -D branch-to-merge
```

## Managing Internal Dependancies

Managing internal dependancies can be tricky depending on your companies current workflow. Even using modern tools it still can 
be a bit of a bottleneck on workflow and can be difficult to manage between the development team and the business.

## Composer

Ideally composer should be used to manage internal PHP dependancies. All dependancies should be listed in your composer.json
file against a tag release of the relevant dependancy. dev-master should be avoided being used including avoiding using other
development/feature/bugfix branches.

The way this works is that if you have a main project that depends on another project of shared classes and you have worked
on both projects to ultimatly create a feature on the main project then you must wait before your pull request on the 
shared project is merged and added into a tag release so you can update the main projects composer.json files tag release.
Once that is update that pull request can be merged into the main project.

This avoids the main project ever ending up becoming broken due to invalid or old dependancies. During active 
development of a feature dependancies should be symlinked in the vendor folder to avoid modifications of the composer.json
file to include development branches. This also removes the risk of those changes being merged which would cause a failure
as soon as the development branch is merged and removed.

## Disadvantages and Benefits

The above composer details can stiffle workflow and slowdown getting jobs done fast, however it can seriously reduce
error rates, and increase trust of application security in the live environments. 

## Code of Conduct

- Generally be good to one another, and don't take life too seriously. 
- Have time for your fellow developer and they will have time for you.
- Respect other developers opinions and they may respect yours.
- Remember that there is always someone out there you can learn from irregardless of yours or their level.