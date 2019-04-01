
# Working with JSON


## Introduction
When you retrieve information from an API, it is often stored in either an XML or a JSON format. In this lesson, we look at what JSON is, the problems it solves, and provide some hands on experience working with it. 


## Objectives
You will be able to:
* Explain what JSON is and why it is used
* Retrieve information from a JSON file and store it in a List or DataFrame

## XML - lite

In the early 2000's, XML was becoming increasingly popular - not only as a data interchange format but also as a configuration format to allow developers to set options and values in their code. However, while XML is powerful, it requires a lot of typing and isn't very readable as so much of the text required in an XML document is the repetition of the field descriptions using tags. Look at the snippet of XML below - 62 characters to convey a 4 character first name!

```
<people>
    <person>
        <firstname>Jane</firstname>
    </person>
</people>
```

Of course, this is a particularly bad example, but XML certainly isn't concise, and that is a problem both for humans having to read and edit XML files and *(in the case of very large files)* for the time it takes to transfer the information from one computer to another.

At around the same time, Douglas Crockford first specified and popularized another format for sending information over the web - it was called JSON (pronounced Jay-son - like the name "Jason"). It is short for JavaScript Object Notation and was originally intended to be part of the JavaScript scripting language, although it was always language agnostic (you can read and write JSON using almost any programming language).

Over time, and especially with the rise in popularity of Javascript for adding functionality to web pages, JSON has become one of the most popular formats for sending and retrieving information from APIs.

As with XML, it can be used to store any kind of information, but is most useful/valuable for representing structured data - and in particular, hierarchical, structured data. It solves the sames problems as XML and stores the same kind of information - just using a different format.

Here's an example of what the "people" xml file from the last lesson would look like in JSON:

```
{
  "people": [
    {
      "firstname": "Jane",
      "lastname": "Anderson",
      "phonetype": "cell",
      "phonenumber": "111-111-1111"
    },
    {
      "firstname": "Joe",
      "lastname": "Sonos",
      "phonetype": "office",
      "phonenumber": "111-111-1111"
    },
    {
      "firstname": "Alison",
      "lastname": "Demming",
      "phonetype": "home",
      "phonenumber": "111-111-1111"
    }
  ]
}
```

Some key things to notice:
* This looks a lot like a Python dictionary
* The `people` key has a value type of list (notice the square brackets) and is a list of JSON objects (which in Python gets stored as a list of dictionaries)
* The key/value pairs are both wrapped in quotation marks (e.g. both `"firstname"` and `"Jane"` have the quotes)
* The "value" of a key value can be another object (another collection of key value pairs) - in the same way that in XML the `<person>` tag could contain a set of sub-tags like `<firstname>` and `<phone>`
* There is not the same concept of "attributes" as in XML. You notice we had to add another key-value pair for the `phonetype`. We could also have created a `phone` object with a `type` and a `number` key - `"phone" : { "type": "cell",  "number": "111-111-1111"}`
* Indentation is useful to ensure you match your opening and closing brackets, but it is not meaningful. The following JSON will work just the same as the example above:

```
{"people": [{"firstname": "Jane","lastname": "Anderson","phonetype": "cell","phonenumber": "111-111-1111"},{"firstname": "Joe","lastname": "Sonos","phonetype": "office","phonenumber": "111-111-1111"},{"firstname": "Alison","lastname": "Demming","phonetype": "home","phonenumber": "111-111-1111"}]
}
```

OK, let's take a run at working with a JSON file. There is a file in this directory called people.json containing the same information as the people.xml file in the last lesson. 

Just as there is an XML standard library in Python, it also contains a [JSON standard library](https://docs.python.org/3.6/library/json.html). 

So we start by importing that library, opening the file and loading the JSON:


```python
import json

json_data = open('people.json')
data = json.load(json_data)
print(type(data))
print(data)

```

As you'll see, the json library loaded this into a Python dictionary that we can now traverse. Lets start by having a look around:


```python
data.keys()
```

Looks like we have a top level key of "people", let's see what's contained within it . . .


```python
print(type(data["people"]))
data["people"]
```

There we go, that's a List of people. OK, lets try to do what we did with the XML file - let's retrieve the phone number for each of the people and put them all in a list . . . 


```python
phone_numbers = []
for person in data["people"]:
    phone_numbers.append(person["phonenumber"])
print(phone_numbers)
```

Great - now lets try to get a list of addresses - lets just take the cities and make a list of them first.


```python
cities = []
for person in data["people"]:
    for address in person["addresses"]:
        cities.append(address["city"])
print(cities)
```

OK, now it's your turn! Instead of creating a List of every city, create a Set of the distinct cities.

Great! Now iterate over the document and create & print a list of first names:

Perfect! Now go in and create a list of the states that the people have an address in:

Now, create a list of full names *(hint, you'll have to concatenate the first name, a space and then the last name for every full name)*:

OK, in practice, you're usually going to want to create a DataFrame from your JSON file. Here is the code to create a DataFrame containing the first and last names for each person:


```python
import pandas as pd

dfcols = ['firstname', 'lastname']
df = pd.DataFrame(columns=dfcols)

for person in data["people"]:
    firstname = person['firstname']
    lastname = person['lastname']
    df = df.append(pd.Series([firstname, lastname], index=dfcols), ignore_index=True)
df.head ()
```

Great, now create a DataFrame that contains the first name, last name and phone for every person:

## Extra Credit (1)
Sometimes you need to perform more complex transformations to create the DataFrame you want. Write some code to create a DataFrame with one row per address, containing the first name, last name and all of the address information for each address *(anyone without an address simply won't show up in the DataFrame)*.

## Extra Credit (2)
There is another file in this directory - `nyc_2001_campaign_finance.json`. Open the file using Python, explore it, and create a DataFrame containing the Candidate Name, Primary Pay, General Pay, Runoff Pay and Total Pay for each candidate.

## Summary

Congratulations! You've now got a bunch of hands on experience of working with both XML and JSON data files. Next up, let's look at how to interact with APIs to be able to retrieve such files!

