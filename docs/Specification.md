# Economy Specification
## Summary

The Uchuumaru economy is strongly inspired by Zuramaru and Kanchou's internal systems. The economy will be used for a multitude of functionalities within Uchuumaru but should remain separate from the primary moderation architecture. It must therefore expose a clean, simple-to-use, and `Result` callback-based API. 

## Goals
The overarching intention of the economy is to provide a sense of reward and competition with users from the **CMI** principle.

### Collection
Collecting currency should be tedious, but the interface to do so should also invoke feelings of satisfaction and achievement.  

### Moderation
The economy should be strictly moderated to ensure inflation does not occur and that a small handful of users do not have an unreasonable amount of currency relative to the little time they put into farming (see the mess of `?hanalb`). 

### Integration
The notion of the "economy" should be tightly coupled with the server as a whole, rather than being perceived as "belonging to Uchuumaru." 

## Currency
There are three central currencies, all of which are part of the same economy: **Banja**, **Luka**, and **Angels**. Please do not perceive them as three separate economies, they are _three currencies of the same economy_ and can be exchanged. 

### Banja
The first part in the name of a city in Bosnia. It is the most common and relatively least valuable currency used for mundane transactions. 

### Luka
The second part in the name of a city in Bosnia. It is more valuable than Banja and is used for generally more significant transactions. 

### Angels
A heavenly presence on Earth. Angels are the most valuable currency and are used for only the most elite transactions. 

### Conversion 
Banja, Luka, and Angels can be converted into one another in most scenarios. For example, it is possible to convert Banja to Luka, but not Luka to Banja. The conversion rates are listed in the table below. 

|        | Banja | Luka |  Angels  |
|:------:|:-----:|:----:|:--------:|
|  Banja |   1   |  0.1 |   N/A    |
|  Luka  |  N/A  |  1   | 0.000001 |
| Angels |  N/A  |  N/A |    1     |

### Code Implementation 
#### Representation
A unique `sealed class` will represent each currency, and each currency will implement an `ICurrency` interface.
```csharp
public interface ICurrency 
{
	string Name { get; }
	int ConvertTo<TCurrency>(int amount) where TCurrency : ICurrency;
}
```

#### Relationships
To enable conversions between currencies, their relationships need to be defined somewhere. It is possible to define them within the `ICurrency#ConvertTo` method, but this would violate the open-close principle and easily lead to unmaintainable code. 

Therefore, a `CurrencyRelationshipsBuilder` with a Fluent API is required . This class will build a `CurrencyRelationships` object that can be added to an `IServiceCollection` so any currency can find its relationships at any time via DI.

> ⚠️ **This is a proof of concept and not a concrete implementation detail**

#### The Relationship Definitions Class
```csharp
public class CurrencyRelationships  
{  
	private Dictionary<(Type To, Type From), double> _conversionFactors = new();  
  
	public double GetConversionFactor<TTo, TFrom>()  
		where TTo : ICurrency  
		where TFrom : ICurrency  
	{  
		return _conversionFactors[(typeof(TTo), typeof(TFrom))];  
	}  
}
```

#### Implementation of ICurrency and DI Usage of CurrencyRelationships
```csharp
public sealed class Banja
{
	private readonly CurrencyRelationships _currencyRelationships;
	
	public Banja(CurrencyRelationships currencyRelationships )
	{
		_currencyRelationships = currencyRelationships 
	}
	
	public string Name => typeof(this).Name;
	
	public int ConvertTo<TCurrency>(int amount) where TCurrency : ICurrency
	{
		var factor = _currencyRelationships.GetConversionFactor<typeof(this), typeof(TCurrency)();
		return Math.Floor((double) amount * factor)
	}
}
```