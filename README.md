# BittrexClient.net
This is a .net wrapper for the Bittrex Api

The wrapper is based on .NET Framework 4.6.1. 

For each api-method there is a blocking and non blocking method available. The non blocking mehtod ends with Async. If there are optional parameters at the api-method, these parameters are also optional, in this case there are method overloads without the optional parameter availabe or you can just set the parameter to 'null' ('Nothing' in VB). For compatibility default parameters are not used, method overloads are made instead.

For everyone who uses ReSharper I added an special Version that includes the ReSharper annotations for better code analysis, therfore the JetBrains.Annotations.dll is nessesary, this contains no logic whatsoever just attributes to decorate your classes/properties/methods to help Resharper analyse your code while developing. If you do not use ReSharper now, you should really consider it.

To use the wrapper you have to

1. Reference the assembly Goldfinger.Bittrex.dll in the project
2. Set the token
3. Call the methods

Please use the issue function on GitHub to file a request/issue/suggestions.

Documentation of the methods are available as inline docu in the Goldfinger.Bittrex.xml. This file can be used to create a msdn like help file with Sandcastle or similar tools.
Visual Studio should pick up the XML and show the documation for each method when using smart completition.

English is not my natural language and writing a wrapper to a (poorly) documented api is one thing understanding what the parameters are doing is an other.
I tried to use the same names for the same thing all around the api planing if I ever write an other wrapper for an other exchange to use the same names, so that a developer who uses my wrappers always know that this name has this meaning regardless how the exchange names it. I have done a lot of guessing there and if someone thinks the naming is off and should be changed please file a suggestion on GitHub.
Localization of error messages is also possible. If someone do want to add a language I can extract the strings for translation.

Minimum blocking example:
```C#
namespace ConsoleApplication
{
	using System;
	using System.Collections.ObjectModel;

	using Goldfinger.Bittrex;
	using Goldfinger.Bittrex.PublicApi;

	internal class Program
	{
		private static void Main(string[] args)
		{
			//For security reasons this is bad, the better way would be to use the SecureString overload.
			//This token is valid for the whole application and has to be set only once.
			//Creating a new token while a other token already exists will override the existing token.
			BittrexAccessToken.New("yourApiKey", "yourApiSecret");

            //This is the blocking call.
			ReadOnlyCollection<Currency> currencies = PublicClient.GetCurrencies();

			Console.WriteLine("All Bittrex Currencies");
			foreach (Currency currency in currencies)
			{
				//If you do not understand this line lookup the new language features of C# on the internet.
				Console.WriteLine($"{currency.CurrencyAcronym.PadLeft(5)}| Name: {currency.CurrencyName}, Type: {currency.CoinType}, Active: {currency.IsActive}, Minimum confirmation: {currency.MinimumConfirmation}, Transaction fee: {currency.TransactionFee:N8}");
			}

			//This will erase the token from the memory.
			//Not realy necessary at the end of the programm.
			BittrexAccessToken.Clear();

			Console.ReadLine();
		}
	}
}
```

Minimum non blocking example:
```C#
namespace ConsoleApplication
{
	using System;
	using System.Collections.ObjectModel;
	using System.Threading.Tasks;

	using Goldfinger.Bittrex;
	using Goldfinger.Bittrex.PublicApi;

	internal class Program
	{
		private static void Main(string[] args)
		{
			//For security reasons this is bad, the better way would be to use the SecureString overload.
			//This token is valid for the whole application and has to be set only once.
			//Creating a new token while a other token already exists will override the existing token.
			BittrexAccessToken.New("yourApiKey", "yourApiSecret");

			//Not very clever to use the blocking Wait() here but I think you get the point.
			Run().Wait();

			Console.ReadLine();
		}

		private static async Task Run()
		{
			//This in the non-blocking call.
			ReadOnlyCollection<Currency> currencies = await PublicClient.GetCurrenciesAsync().ConfigureAwait(false);
			Console.WriteLine("All Bittrex Currencies");
			foreach (Currency currency in currencies)
			{
				//If you do not understand this line lookup the new language features of C# on the internet.
				Console.WriteLine($"{currency.CurrencyAcronym.PadLeft(5)}| Name: {currency.CurrencyName}, Type: {currency.CoinType}, Active: {currency.IsActive}, Minimum confirmation: {currency.MinimumConfirmation}, Transaction fee: {currency.TransactionFee:N8}");
			}
		}
	}
}
```
