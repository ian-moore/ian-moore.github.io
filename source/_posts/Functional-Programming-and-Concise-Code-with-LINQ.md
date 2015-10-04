title: Functional Programming and Concise Code with LINQ
tags:
  - 'c#'
  - functional programming
  - clean code
date: 2015-10-04 18:45:43
---

I recently came across a blog post titled "[Increase code conciseness with functional programming](http://jsforallof.us/2015/08/10/increase-code-conciseness-with-functional-programming/)" as it relates to javascript, and was interested in doing a quick comparison of the same concept in C#.

One of the most powerful and unique features of C# is LINQ, and once you are familiar with all of the functions - especially `Where` and `Select` - it can make dramatic reductions in the number of lines you need to write. Having already known LINQ before I began reading about functional programming, it was obvious that there is a clear connection between the two.

To illustrate, let's translate the above post into C#.
## Data Setup
We have two collections of users, and want to find the data in `otherUsers` that is not part of `originalUsers`. Once we have the new users, we will pass them to an arbitrary `MakeUser` function.
```
var originalUsers = new List<User>()
{
    new User { Name = "Lucille" },
    new User { Name = "Gob" },
    new User { Name = "Buster" }
};

var otherUsers = new List<User>()
{
    new User { Name = "Lucille" },
    new User { Name = "Gob" },
    new User { Name = "Buster" },
    new User { Name = "Michael" }
};
```
## Procedural Approach
Approaching this in a procedural/imperative manner, you might do something like this - with some nested `foreach` loops to compare the data and pick out what is different:
```
// Find users not present in the original list
var newUsers = new List<User>();
foreach (var otherUser in otherUsers)
{
	var foundUser = false;

	foreach (var user in originalUsers)
	{
		if (user.Name == otherUser.Name)
		{
			foundUser = true;
		}
	}

	if (!foundUser)
	{
		newUsers.Add(otherUser);
	}
}

foreach (var user in newUsers)
{
	MakeUser(user);
}
```
This works perfectly, but isn't very clean. For something so simple, it's unnecessarily complex: lots of temporary variables, nested loops and conditions, and it's pretty long for only 1 real meaningful section (the last loop).

## Functional Approach with LINQ
By transforming our logic into LINQ expressions, we can convert our procedural looping and conditionals into simple functional expressions.

```
foreach (var user in otherUsers.Where(o => !originalUsers.Any(u => u.Name == o.Name)))
{
	MakeUser(user);
}
```
This reduces all of the above code into 3 lines, and 1 loop. I find this much easier to understand - as long as you know LINQ, it's very obvious what this one line of code does. Approaching C# from a functional perspective and utilizing LINQ has really helped clean up the code I write.

**Sidenote: But why is there still a foreach loop?**

Yeah, it's very possible to replace the last loop with something like: `.Select(u => MakeUser(u))` however LINQ collections are lazily evaluated, which means you wouldn't actually be calling your function until iterating through the list. This is why only the List type has a `.ForEach` function, as it's already a defined set.
