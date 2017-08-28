# Stop Object Reference Errors in their Tracks

:: focus on 
 - proper domain modeling
 - validation
 - using Option instead of null
 - Either instead of throw Exception, Primitive obsession



## Domain Modeling

A Vehicle will always have:
 - Vin
 - StockNumber

A Vin:
 - Is always 17 characters
 - Has other characteristics that can be validated

 A StockNumber:
 - Is always a 'long'
 - Has 10 numbers

 Once a Vin object enters our Domain, it will always have those characteristic.
 If it does not, it CANNOT enter the domain. If we let it, it may/will corrupt our domain.

 An API is the main line of defefense in keeping data in our databases correct.

 When it comes to the 'Inventory' domain, a vehicle may or may not have a StockNumber. 
 We may want to represent a vehicle that 

 ```
//vehicle purchased from auction
purchasedRepository.Insert(vehicleJustPurchased)

//vehicle goes through IC
vehicleReadyForSale.Insert(vehicleWithStockNumber)

```

Its possible that we could use two different vehicle objects:

```csharp
class Vehicle {
	string Vin;
}

class VehicleReadyForSale {
	string Vin;
	long StockNumber;
}
```

But then we could corrupt our domain very easily. A new developer could unknowingly:

```csharp
var vehicle = new VehicleReadyForSale() {
	Vin = "12345678901234567",
	StockNumber = 0
}
```

Or even worse:

```csharp
var vehicle = new VehicleReadyForSale() {
	Vin = "12345678901234567"
}

vehicleReadyForSale.Insert(vehicle);
```

Now, some other system reading the data out, starts exploding because there are NULLS!

You might now say ... But the DBA should have ensured the column wasn't nullable!

YOU SHOULD HAVE PREVENTED IT IN THE FIRST PLACE.

Lets look at another problem:

```csharp
class VehicleJustPurchased {

}

class VehicleReadyForSale {

}
```

These classes are incompatible. So you can use one in one part of the domain, and one in another part.

This may not be a bad thing, but at some point, it will cause a problem:

```csharp
//what about the case of a 'kick' or where we bought it but it was crap and we need to auction it back
void SellVehicleAtAuction(VehicleReadyForSale vehicle){
	
}
//Lets now overload the method with `VehicleJustPurchased`???
void SellVehicleAtAuction(VehicleJustPurchased vehicle){
	//Make sure StockNumber doesnt get called... otherwise your up for a bad time
}
```

Lets create a base class. How do we do that?

```csharp
class Vehicle {
	string Vin;
	// Can we add a StockNumber here? Its not valid in some cases.
}

class VehicleJustPurchased : Vehicle {
	//string Vin;
}

class VehicleReadyForSale : Vehicle {
	//string Vin;
	long StockNumber;
}
```

Lets now think of SOLID: What does the 'L' mean?

Liskov Substitution Principle:
"If it looks like a duck, quacks like a duck, but requires batteries... You pobably have the wrong abstraction."
https://stackoverflow.com/questions/56860/what-is-the-liskov-substitution-principle

But whats the right abstraction! Its a damn car! We just need to sell it!

```csharp
void SellVehicleAtAuction(Vehicle vehicle){
	//Make sure StockNumber doesnt get called... otherwise your gonna have a bad time
}
```

Lets let this idea rest for a minute.


## APIs are Contracts, Dont Violate Them

```csharp
IOU CreateIou(Person you, Person person, Money money){
	return _repository.Create(you, person, money)
}

PayableIou(IOU iou) {
	return _repository.FindPayableIou(iou);
}

someIou = CreateIou(me, you, $100)
var iou = PayableIou(someIou);
iou.PayMe()
// throw NullReferenceException()
//Explosion with really ugly fireworks
//Were not friends any more
// Haha, F***Y**, I'm a contract, but doesn't mean I'm gonna give you your sh** back
```

Sorry, but I really dont like people like this. Nor do I like interfaces like this.

Oh wait... you just weren't ready to pay?!?!?

Why didn't you just tell me that!!!

```csharp
Maybe<IOU> iou = PayableIou(someIou);  //Maybe and Option mean the same thing

var response = iou.Match(
	SomePayment: payment => $"Thanks for paying me back ${payment}",
	NoPayment: () => $"Uh, dude you owe me money {someIou.payment}"
)
```

Cool, at least you let me know you may or may not have been able to pay. No one likes an empty answer.

Let's be cool when we answer. If we can deliver on what was asked, lets let them know.

Boss says: "Give me the report"
You say: "Your a d***, its not done, AHHHHH This is hard stuff!!!"

Instead:

Boss says: "Hey hows the report going"
You say: "Good, but I have a couple details to finish up, I'll get it to you by 5."

Lets provide our APIs the ability to return good information the users.

## Be Nice, Return Valid Information, Even if you dont have it

Dont return ‘null’ when you said youd return an item.

```csharp
//interface
Item = find(Integer)

//use
item = find(123);

item.Name
//BOOM Goes the dynomite
```

Lets make it explicit in our contracts that something may not be exposed.

```csharp
//interface
Maybe<Item> = find(Integer)

//implementation
maybeItem = find(123);
maybeItem.Match(
	Some: (item) => item + 1,
	None: () => 0);
```

Curt is smarter than me, and knows when a null value could be returned.

```csharp
DoSomething(Vehicle vehicle) {
	if (vehicle.StockNumber.HasBeenCancelled()){
		//Explosion.... Stocknumber could be null. Why didn't you assume that dummy??	
	}
}
```

Oh didn't know StockNumber could be null. 
Why wasn't that just made clear in the first place!!
I wouldn't have done it otherwise.
Now I can go debug for an hour trying to figure out my its null.
Thanks. Now I wasted an afternoon trying to figure this out.
Oh sh** nevermind, it was my own fault. I didnt this to my self cause I mapped the object wrong.
//Usually it's Curts fault, so I pointed the finger at him

So maybe the Maybe thing is an OK thing. 

Lets figure out how to use it.

Lets forget we are forgetful:

```csharp
var vehicle = _repo.Vehicles(123);
vehicle.StockNumber.HasBeenCancelled();
//Boom! Oops, would be valid if it wasnt a kick, but it was and so we'll now need to redeploy
```

```csharp
var vehicle = _repo.Vehicles(123);
vehicle.StockNumber
	.Match(
		Some: stock => stock.HasBeenCancelled(),
		None: () => false
	);
//No more Boom Boom. Took care of the case where the vehicle had neve been sold.
```

What the hell is this `Match` thing????

```csharp
var vehicle = _repo.Vehicles(123);
var stockNumber = vehicle.StockNumber
if (stockNumber == null)
	return false
else
	return stock.HasBeenCancelled()
```

Notice though how the enforcement is explicit vs implicit. Meaning, you MUST do it vs. you SHOULD do it.

This explicitness makes you aggressively handle the negative cases.
Many times, it is our laziness that allows 'things to slip through the cracks'.


Lets back track... Whats wrong with this code?

```csharp
var vehicle = _repo.Vehicles(123);
vehicle.StockNumber
	.Match(
		Some: stock => stock.HasBeenCancelled(),
		None: () => false
	);
//No more Boom Boom. Took care of the case where the vehicle had neve been sold.
```

If `_repo.Vehicle(123)` returns null, what do we do??

```csharp
var vehicle = _repo.Vehicles(123);
vehicle.StockNumber
	.Match(
		Some: stock => stock.HasBeenCancelled(),
		None: () => false
	);
//No more Boom Boom. Took care of the case where the vehicle had neve been sold.
```

So lets protect against that too!

Are you an engineer? In the last talk I asked:

'Customer' walks up and says: "The X site isn’t responding!!!"
You said: “It can’t be us!” "Its working fine for me!"
Then you: Tried to find the issue by digging through logs...
And finally, make a recommendation on the 'cause': "Its not my fault"


Again, lets use good engineering practices.

You are not a bad engineer if you use the traditional:

```csharp
var obj = _repository.Find(123)
if (obj == null)
	return obj
else
	throw CauseIDontKnowWhatElseToDoException("FML and yours, hahahah");

```

Just realize there may be something better. 
Somthing that makes you waste less time on debugging and garbage.


One More Example:

Search for object and put it into another object
//This will illustrate where the real danger of this comes into play

```csharp

var dao = _repository(1);
Person person = Map(dao); 

//person now has a time bomb in it. Tick tock, tick tock....

Person Map(RepoDao dao)
	return new Person(){
		Name = dao.Name,
		PersonType = FindInDict(1)  //TODO: needs improvement
	}
}

string FindInDict(int i) {
	//find in dictionary, 
	//may return a null, 
	//if so good luck, cause you'll never know it doesn't exist
	//unless you check
}
```


## Either

So an `Option` (or `Maybe`in JS) type can be used to validate whether something exists.

```csharp
Option<Vehicle> Validate(string Vin, string stockNumber){
	if (vin == null)
		return None;
	return new Vehicle(vin, stockNumber);
}
```

Haha, screw you. You supplied something wrong, but I'm not going to tell you what!!!
Ran into this with the SocketLabs Api. They said "You didnt supply the correct body"
Whay about it!!!

What happens if it is `false`. You need to know why??

So lets return a reason why:

```csharp
Either<string, Vehicle> Validate(string vin, long stockNumber){
	if (vin == null)
		return "Vin is null";
	if (vin.length != 17)
		return "Vin is invalid length";
	if (stockNumber == null)
		return "StockNumber is null"
	if (stockNumber.lenght != 10)
		return "StockNumber is an invlid length"
	return new Vehicle(vin, stockNumber);
}
```



## Constructing Domain Objects

### Validation

```csharp
var dto = new VehicleDto() {
	Vin = "12345678901234567",
	StockNumber = 0123456701
}
var vehicle = Map(dto); 

Vehicle Map(VehicleDto dto) {
	if (dto.Vin == null)
		throw new ArgumentNullException("Vin is null");
	
	if (dto.StockNumer = null)
		throw new ArgumentNullException("StockNumer is null");
	return new Vehicle(){

	}
}

```

This validation code is:
 - Unusable anywhere else because it sits in the API layer
 - Only a DTO object can be passed in as an argument
 - Only one validation message can be returned at a time
 - Throws exception, but in your API you probably want to handle as a 400, not 500


 ```csharp

Either<List<Error>, Vehicle> Map(VehicleDto dto) {
	return Vehicle.New(dto.Vin, dto.StockNumber)
}

class Vehicle() {
	private Vehicle(Vin vin, StockNumber stockNumber) {
		Vin = vin;
		StockNumber = stockNumber;
	}
	Either<List<Error>, Vehicle> New(string vin, string stockNumber) {
		return from _vin in Vin.New(vin)
			   from _stockNumber in StockNumber.New(stockNumber)
			   select new Vehicle(_vin, _stockNumber);
	}
}
class Vin() {
	private Vin(string vin) {

	}
	Validation<List<Error>, Vin> New(string vin){
			
	}
}
 ```

```csharp
// use either to validate incoming data from APIs
apiResponse = HTTP.GET('url');


```

Now we can:
 - Validate Vehicle and Vin and StockNumber ANYWHERE in the Domain
 - Any data coming from the Database, API calls, etc can be validated before 'entering the domain'


Ensure theres validation when creating Domain objects. You’re going to complain: “But theres so much more code to write, which has more opportunity for bugs.” or “its such a pain to write all that code”
Or more likely you’ll just be lazy and not write the validation code. But WHEN that code fails because you didnt write it, slap your self and go write it. If you spend an hour to write it, it will save the next person an hour tracing down where the bug is coming from.








Use factories to create complex objects
Do validation on all incoming data

More specific Eithers- Validation, Exceptional C#_6.5.4
Stop throwing exceptions- all possible code paths are explicit

Legos and reusability - picture of legos and duplos connected with duct tape and superglue

Notes:
Reduce cyclomatic complexity with Match
Move up one level of abstraction, dont think of each item in a list, rather think of the list and how you want to transform the item from one type to another
Instead of checking item exists, assume it will and wont be there, use your tools to FORCE you to check for the conditions.

Constructing domain objects:
Ensure theres validation when creating Domain objects. You’re going to complain: “But theres so much more code to write, which has more opportunity for bugs.” or “its such a pain to write all that code”
Or more likely you’ll just be lazy and not write the validation code. But WHEN that code fails because you didnt write it, slap your self and go write it. If you spend an hour to write it, it will save the next person an hour tracing down where the bug is coming from.





Lets be like Lawyers for a minute:
An interface is a type of contract. 
When you create a contract, you specify ‘when I give you this, you’ll give me that’. But what happens when one party changes the contract? Things break! People get hurt!
If we know something contractually can go wrong, we should lay it out in the contract, and spell out what happens in the case of a breach of contract. So... if you default (don’t pay), then you will be obligated will charge you extra money.





Forgotten Null Checks
Give example of a repository call and a missing null check. Show how easy it is to forget the call. 
```
Customer customer = _repository.GetById(id);
Console.WriteLine(customer.Name);
```
Make several calls to different repositories and combine the results into one object. Question: what do you do if there are null values? Throw an error? Expect the client to check?
Use Motion code examples for this??

Use Option (.NET land) or Maybe (JS)
```
Maybe<Customer> maybeCustomer = _repository.GetById(id);
maybeCustomer.Match(
	Some: customer => Console.WriteLine(customer.Name)
	None: () => //report error?,);

//Other less safe implementation
if (maybeCustomer.IsSome)
	Console.WriteLine(maybeCustomer.Value.Name)
else
	//report error
//Why is it unsafe… we the lazy developer will just do
Console.WriteLine(maybeCustomer.Value.Name)
```
This **enables the API developer to communicate with the API consumer** that this function may return an empty value. `null` doesn’t communicate anything and its a hidden case.
