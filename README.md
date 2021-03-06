# Tababular

A simple .NET monospace text table formatting library for .NET 4.5 and .NET Standard 1.6.

You can use it if you are standing with a bunch of objects or dictionaries in your hand, and you
wish for them to become as nice as this:

	============================================
	| FirstColumn | SecondColumn | ThirdColumn |
	============================================
	| r1          | hej          | hej igen    |
	============================================
	| r2          | hej          | hej igen    |
	============================================

This can easily be achieved by newing up the `TableFormatter` like this:

	var formatter = new TableFormatter();

and then you call an appropriate `Format***` method on it, e.g. `FormatObjects`:

	var objects = new[]
	{
		new {FirstColumn = "r1", SecondColumn = "hej", ThirdColumn = "hej igen"},
		new {FirstColumn = "r2", SecondColumn = "hej", ThirdColumn = "hej igen"},
	};

	var text = tableFormatter.FormatObjects(objects);

	Console.WriteLine(text);

which will result in the table shown above.

For now, Tababular does not support that much stuff - but one nice thing about it is that
it will properly format lines in cells, so that e.g.

	var objects = new[]
	{
		new  { MachineName = "ctxtest01", Ip = "10.0.0.10", Ports = new[] {80, 8080, 9090}},
		new  { MachineName = "ctxtest02", Ip = "10.0.0.11", Ports = new[] {80, 5432}}
	};

	var text = new TableFormatter().FormatObjects(objects);

	Console.WriteLine(text);

becomes nice like this:

	===================================
	| MachineName | Ip        | Ports |
	===================================
	| ctxtest01   | 10.0.0.10 | 80    |
	|             |           | 8080  |
	|             |           | 9090  |
	===================================
	| ctxtest02   | 10.0.0.11 | 80    |
	|             |           | 5432  |
	===================================

which looks pretty neat if you ask me.

# Formatting different things

Tababular can format different things, which at the moment includes:

* Objects: `formatter.FormatObjects(objects)`
* Dictionaries: `formatter.FormatDictionaries(dictionaries)`
* JSON: `formatter.FormatJson(jsonText)`

# More niceness

What about longs texts? Consider this example where the "Comments" column can be used to supply arbitrarily long texts:

	var objects = new[]
	{
		new {MachineName = "ctxtest01", Ip = "10.0.0.10", Ports = new[] {80, 8080, 9090}, Comments = ""},
		new {MachineName = "ctxtest02", Ip = "10.0.0.11", Ports = new[] {5432},
			Comments = @"This bad boy hosts our database and a couple of internal jobs."}
	};

	var text = new TableFormatter().FormatObjects(objects);

In this case, the resulting table would look like this:

	====================================================================================================
	| MachineName | Ip        | Ports | Comments                                                       |
	====================================================================================================
	| ctxtest01   | 10.0.0.10 | 80    |                                                                |
	|             |           | 8080  |                                                                |
	|             |           | 9090  |                                                                |
	====================================================================================================
	| ctxtest02   | 10.0.0.11 | 5432  | This bad boy hosts our database and a couple of internal jobs. |
	====================================================================================================

which might be fine, but since the texts can be even longer than this, it might end up being a problem.

Fear not! We can supply a small hint to the table formatter like this:

	var hints = new Hints { MaxTableWidth = 80 };
	var formatter = new TableFormatter(hints);

and then when we

	var text = formatter.FormatObjects(objects);

it will look like this:

	====================================================================================
	| MachineName | Ip        | Ports | Comments                                       |
	====================================================================================
	| ctxtest01   | 10.0.0.10 | 80    |                                                |
	|             |           | 8080  |                                                |
	|             |           | 9090  |                                                |
	====================================================================================
	| ctxtest02   | 10.0.0.11 | 5432  | This bad boy hosts our database and a couple   |
	|             |           |       | of internal jobs.                              |
	====================================================================================

and the table will never become wider than at most 80 characters. Objectively speaking, this is actually freaking awesome.

# License

[The MIT License (MIT)](http://opensource.org/licenses/MIT)