	Python is a multipurpose high level programming language with a design that emphasizes code readability. Python syntax is built to express code found in C++ or Java using fewer lines.  As with many languages, Python continues to add features and fix bugs in old features in order to improve the development experience.  Development in Python is done primarily using the Python Enhancement Proposal (pep) process.  In any given pep, various changes to the language are proposed by the community or by Guido van Rossum (It should be noted that Python is Guido van Rossum's language which means he gets the final decision on whether or not to implement a pep). These changes are discussed and reviewed before they can become a part of the Python language. One pep that made it into the language - pep 0308 - was a feature designed to give Python a new ternary operator. Pep 0308 came about after many developers in the Python community complained about confusing if/else statements and expressed a desire for a shorter, cleaner method.
	In Python version 2.4 (before pep 0308), conditional events were often handled using if/else statements.  This led to lots of code like the following[4]:
''''Python
if condition:
    var = value if true
else:
    var = value if false
''''
While fairly straightforward, this example of an if/else statement is far more verbose than a ternary operator.  Having to use this syntax a few times in a file is fine, however the code becomes difficult to read and reason about when many conditional statements or nested conditional statements are needed.  Languages such as C++ and Java use the ?: ternary operator to simplify conditional expressions[1][2]. An example syntax in Java is as follows:
''''Java
	variable x = (condition) ? value if true : value if false 
''''
Comparing the two examples shows that a ternary implementation of the same code is far less verbose than an if/else statement.  However both examples are functionally equivalent.  If the condition is true then the variable takes on the "value if true". If the condition is false then the variable takes on the "value if false".  Furthermore both examples are short-circuited: that is to say that only one of the paths is evaluated. For example if the condition is true, the "value if false" statement will not be evaluated.  It should also be noted that the advantage of a ternary operator over an if/else statement is almost entirely in the readability of code. There isn't a discernible performance difference a majority of the time [3]. Aside from if/else statements there was a was to create conditional expression using tuples ans lists, but it had significant disadvantages. The following is an example of a conditional expression in Python[5]:
''''Python
sColorName = ("Black","White")[Color == COLOR_WHITE]
''''
In this example the "value if true" is "Black", the "value if false" is white and the condition is "[Color == COLOR_WHITE]". If the condition evaulates the true the entire expression simplifies to:
''''Python
sColorName = ("Black","White")[1]
''''
which then leads to:
''''Python
sColorName = "White"
''''
While this works for simple examples, it comes with drawbacks. The first drawback to this implementation is that it involves unpacking the tuple, which has performance implications. The second drawback is that all of the possible values must be valid which prevents short circuiting. This can cause problems like the one in the code example below[5]:
''''Python
r = (DEFAULT_RESULT, Database.Results[index])[IsValid(index)]
''''
If the "index" is not valid in the above example, the code will fail. The lack of short circuiting means that both the "DEFAULT_RESULT" and the "Database.Results[index]" must be valid. Thus pep 0308 was proposed in order to simplify code and implement C++ like conditional expressions, but in a flexible and "Pythonesque" way[5].
	The initial proposal for pep 0308 was rather open-ended because Guido van Rossum was "neither in favor nor against" the proposal[6]. However he still authored the original pep and proposed the following syntax:
''''Python
<expression1> if <condition> else <expression2>
'''' 
It should be noted that in this example <condition> is evaluated first! If <condition> evaluates to true, then <expression1> is evaluated and is the result of the whole example. If <condition> evaluates to false, then <expression2> is evaluated and is the result of the whole example. Guido can Rossum proposed this syntax for two main reasons. First, only one expression is ever evaluated. A short circuited expression keeps the coding simple and has performance benefits[6]. On more of a opinionated note, Guido van Rossum liked that it didn't include any new key words and would be relatively simple to implement. The expression was slightly confusing to some as the conditional statement was in the middle, which meant it can't be read from left to right.  
	Following the initial proposal, the community split into many factions based on what they thought would be the best ternary operator.  Perhaps the most popular, and also first rejected, was to implement the ternary operator exactly like it is in C++ and Java.
''''Python
<condition> ? <expression1> : <expression2>
''''
This evaluates the <condition> first. If the condition is true then <expression1> is evaluated and becomes the result. Otherwise <expression2> becomes is evaluated and becomes the result.  Many of the developers from a C/C++ background voted for this implementation based on familiarity.  Other advantages to the C++ ternary operator include being able to read if from left to right and short circuit evaluation.  Guido von Rossum rejected this for several reasons: for people not familiar with C/C++, this operator can be difficult to understand; next the colon already had multiple uses in Python, and he didn't want to make the colon more confusing. For similar reasons, the following proposed syntax were also vetoed[7]:
''''Python 
<condition> ? <expression1> ! <expression2>
<condition> ? <expression1> , <expression2>
<condition> ? <expression1> else <expression2>
''''
Many possible options were eliminated based on a lack of short circuiting. The following syntax is one example:
''''Python
ifelse(<condition>,<expression1>,<expression2>)
''''
Like many others it read left to right and is simple to reason about. The main advantage here is that it didn't require a new Python built-in, making it easier to add to the Python language.  Unfortunately making it a function meant that both <expression1> and <expression2> would need to be evaluated and substituted before the function was called. This makes it impossible to short circuit and in certain cases can lead to validity issues as discussed earlier[8]. One possible work around to this problem involved using the same syntax, but creating a special form that would evaluate the expressions depending on the value of the first condition. While functional, this idea simply didn't get have enough people supporting it and was thus discarded.
	The next seriously considered option came in the form of
''''Python
if <condition> then <expression1> else <expression2>
''''
It had most of the main components that developers were looking for: left to right evaluation, short circuiting, and was easy to reason about. The main problem with this form was the creation of a new keyword “then”. Guido van Rossum was hesitant to create a new keyword with an arbitrary meaning for a pep that he was uncertain about in the first place. Some developers also thought that it could be difficult to add because the parser would believe that it's an 'if' statement. The following example represents such a case.[6]
''''Python
if verbose then sys.stdout.write(“hello\n”) else None
''''
In order to prevent a parser error, parenthesis would be necessary. This led to the final contender.
''''Python
(if <condition>: <expression1> else: <expression2>)
''''
Unlike the previous case, this form doesn't require a new keyword and prevents any sort of parser difficulty by adding parenthesis around the expression. Beyond that it follows standard Python syntax, has left to right evaluation and is short circuited. The drawbacks are minor: the parenthesis make it look a little odd and the colon is typically at the end of a line.
	After much deliberation Guido van Rossum called for a vote with sixteen possible choices to vote on. The winner would be the option with a clear majority. The vote was uneventful and no option gained a clear majority. Thus Guido picked the original form he proposed stating that it was still his language after all.
''''Python
<expression1> if <condition> else <expression2>
'''' 
	The new ternary operator was given a priority lower than that of 'or' but higher than that of 'lambda'[6].  The following code examples explain how it works.
''''Python
<expression1> if <condition1> else <expression2> if <condition2> else <expression3>
''''
can be read as
''''Python
<expression1> if <condition1> else (<expression2> if <condition2> else <expression3>)
''''
where the code inside the parenthesis is evaluated first[9]. It should be noted that code like this is slightly frowned upon as it makes the code difficult to read, which negates the one of the main points of the ternary operator.
''''Python
<expression1> or <expression2> if <condition2> else <expression3>
''''
can be read as
''''Python
(<expression1> or <expression2>) if <condition2> else <expression3>
Again, the ternary operator has lower precedence than the 'or' operator. 
''''Python
lambda: <expression1> if <condition2> else <expression2>
''''
can be interpreted as
''''Python
lambda: (<expression1> if <condition2> else <expression2>)
''''	
This makes sense as the ternary operator has a higher order of precedence than that of lambda.
	The new syntax nearly created minor backwards incompatibility with Python versions before Python 2.5. In versions 2,4 and earlier, the following legal:
''''Python
[f for f in lambda x: x, lambda x: x**2 if f(1) == 1]
''''
Effectively a list comprehension where a sequence following 'in' is a series of lambdas without parenthesis. For this reason Python version 3.0 requires a series of lambdas to be parenthesized[9]. For example:
''''Python
[f for f in (lambda x: x, lambda x: x**2) if f(1) == 1]
''''
This is due to the fact that lambda binds less tightly than the if-else expression, but could already be followed by an 'if' keyword (which binds even less tightly). On the other hand, Python 2.5 uses a slightly different grammar that prevents lambda from being used in this position by forbidding the lambda body from containing a conditional expression without parenthesis[9]. Several examples are as follows:
''''Python
[f for f in (1, lambda x: x if x >= 0 else -1)]    # OK
[f for f in 1, (lambda x: x if x >= 0 else -1)]    # OK
[f for f in 1, lambda x: (x if x >= 0 else -1)]    # OK
[f for f in 1, lambda x: x if x if x>= 0 else -1]  #Invalid
''''
	The addition of a ternary operator to Python came from community desire and error-prone attempts to achieve the same effect using 'or' and 'and'. A ternary operator can also clean up simple if/else statements by condensing them to one line. After debating what syntax the ternary operator should take, there was a general consensus that it should read left to right, include short circuiting, be 'Pythonesque', and not include any new key words. Many options were presented by various Python developers, but no one option was clearly better than the others. After an indecisive vote, Guido decided to implement his personal choice, which had the above characteristics aside from not reading left to right. In the end, the addition of a ternary operator to the Python language was well received by the community and allowed for cleaner, simpler code.

Sources cited so far:
[1]http://www.cplusplus.com/doc/tutorial/operators/
-ternary operators c++

[2]http://www.tutorialspoint.com/java/java_basic_operators.htm
-ternary operator JAVA

[3]http://stackoverflow.com/questions/18767458/advantage-of-ternary-operator-over-if-else
-ternary operator vs if/else performance

[4]http://stackoverflow.com/questions/3091316/Python-conditional-ternary-operator-for-assignments
-ternary operator advantage Python

[5]http://bytes.com/topic/Python/answers/33461-alternative-suggestion-conditional-expressions-see-pep-308-a
-motivation for ternary operator, way to implement in Python before 0308

[6]https://github.com/Python/peps/commit/5fd591e0b5aa35d7e9e040f4b5464da9ad533700
-inital pep 0308 proposal

[7]http://grokbase.com/t/Python/Python-list/032kb8f178/pep-308-eriks-alternatives-list
-all the voting options

[8]http://Python-checkins.git.net/2003-February/033698.html
-vetoed ifelse(c,e,e) for lack of short circuiting

[9]http://legacy.Python.org/dev/peps/pep-0308/
-official pep 308 documentation






