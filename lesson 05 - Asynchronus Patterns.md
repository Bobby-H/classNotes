# Lesson 5 - Asynchronous patterns

## Recap
- Last week we talked about functions
	- function
	- scope
	- closures
- We covered passing a function into another function
	- We saw how this works with Arrays (filter, forEach, map, reduce, find)
	- Much of the work in a JS app is managing and manipulating data
	- API's return data, app state is data, app views need data


#Intro
- Today we'll quickly review working with Array data
	- This is important when working with API data
- We'll be covering Asynchronous programming patterns. These are super common in UI development like React, jQuery, or even plain JS.

## Array function review

### Iterating

```javascript
//Iterating over Arrays using for loop and forEach
var teachers = ['Assaf', 'Shane', 'Zack']

teachers.forEach(function(item, index) {
	console.log(item, index);
})
```


### Filter
Sometimes you want to filter a data set based on some criteria. The Array object supports a .filter function. It takes a filter function that returns a pass value of true or false.

```javascript
var students = [
	{name: 'Cam Newton', average: 90},
	{name: 'Ted Ginn', average: 58},
	{name: 'Greg Olsen', average: 82}
];

var passingStudents = students.filter(function(student){
	return student.average > 50;
});

passingStudents === [
	{name: 'Cam Newton', average: 90},
	{name: 'Greg Olsen', average: 82}
];

```

## Find
Sometimes you want to find the first item in the array that meets a certain criteria. Its the single version of filter.

```javascript
var students = [
	{name: 'Cam Newton', average: 90},
	{name: 'Ted Ginn', average: 58},
	{name: 'Greg Olsen', average: 82}
];

var passingStudents = students.find(function(student){
	return student.average > 50;
});

passingStudents === [
	{name: 'Cam Newton', average: 90}
];

```

## Map
Sometimes you want to transform data.
The Array's Map function lets you iterate over an array and produce another array with a new value for each item.

```javascript
var students = [
	{firstName: 'Cam', lastName: 'Newton'},
	{firstName: 'Ted', lastName: 'Ginn'},
	{firstName: 'Greg', lastName: 'Olsen'}
]

var fullNames = students(function(student){
	return student.firstName + ' ' + student.lastName;
})

fullNames === ["Cam Newton", "Ted Ginn", "Greg Olsen"]

```


## Reduce
Sometimes you want to calculate a single value based on all the items in an array
The Array's Reduce function let's you iterate over an array and calculate a value.

```javascript
var students = [
	{name: 'Cam Newton', assignmentsCompleted: 6},
	{name: 'Ted Ginn', assignmentsCompleted: 10},
	{name: 'Greg Olsen', assignmentsCompleted: 8}
]

var totalAssignments = students.reduce(function(sum,current){
	return sum + current.assignmentsCompleted;
}, 0);

totalAssignments === 24;

```

## Exercise 1: Array Functions

Using the following data:

```javascript
var players = [
	{firstName: 'Cam', lastName: 'Newton', position: 'QB', touchdowns: 32},
	{firstName: 'Derek', lastName: 'Anderson', position: 'QB', touchdowns: 0},
	{firstName: 'Jonathan', lastName: 'Stewart', position: 'RB', touchdowns: 12},
	{firstName: 'Mike', lastName: 'Tolbert', position: 'RB', touchdowns: 8},
	{firstName: 'Fozzy', lastName: 'Whittaker', position: 'RB', touchdowns: 3},
	{firstName: 'Ted', lastName: 'Ginn', position: 'WR', touchdowns: 9},
	{firstName: 'Devin', lastName: 'Funchess', position: 'WR', touchdowns: 2}
];
```

- Find a player with the name 'Mike'
- Get an array of all players with position 'RB'
- Get an array of all the players lastNames
- Get an array of the full names of all the runningbacks with more than 5 touchdowns
- Get the number of touchdowns scored by Runningbacks

## Exercise 1: Array Functions Answer

```javascript

//Players named 'Mike'
players.find(function(p){
	return p.firstName === 'Mike'
})

//Running backs
players.filter(function(p){
	return p.position === 'RB';
})

//LastNames
players.map(function(p){
	return p.lastName;
})

//Full names of all running backs with more than 5 touchdowns.
players
	.filter(function(player){
		return player.touchdowns > 5 && player.position == 'RB';
	})
	.map(function(player){
		return player.firstName + ' ' + player.lastName;
	});

//Number of touchdowns by runningbacks
players
	.filter(function(player){
		return player.position =='RB';
	})
	.reduce(function(total, player){
		total += player.touchdowns;
	},0)

```

## Asynchronous Programming

In computer programs, asynchronous operation means that a process operates independently of other processes, whereas synchronous operation means that the process runs only as a result of some other process being completed or handing off operation.

TODO: Explain why async matters

### Callback pattern
The passing of a function into another function to be run at a later time is called the "callback pattern". It's prominent in event based programming, which is what UI Development is all about.

Do something later

```javascript
setTimeout(function(){
	console.log('later')
},2000);

console.log('now');
```

Do something if a button gets clicked

```javascript
button.addEventListener('click', function(){
	alert('click');
})
```

Do something when an API request comes back (getData is a hypothetical function)

```javascript
getData('/my-api/data', function(data) {
	console.log('got data', data)
});
```

### This with async functions
The above example is pretty straight forward, but can get tricky when functions are passed around in asynchronous functions. You will, without doubt, make a mistake like this the first time you write a JS application:

```javascript
var teacher = {
	name: 'Shane',
	speak: function() {

		//Maybe you're fetching data from an API, or getting user input
		setTimeout(function(){
			console.log('later my name is ' + this.name);
		},1000)

		//Runs immediately
		console.log('Now my name is ' + this.name);
	}
}

teacher.speak();
```

When you register a function to happen later using something like `setTimeout()`, the window object ends up running the function.

### Assigning Context
Often times, like the example above, you need access to the calling scope or want to assign a specific scope. There are a few different ways to do this.

#### Closure hack
```javascript
var teacher = {
	name: 'Shane',
	speak: function() {

		//Save this to a variable
		var self = this;

		//self is visible inside function because of closure
		setTimeout(function(){
			console.log('later my name is ' + self.name);
		},1000);
	}
}
```

#### Function.Bind
Explicity sets the `this` value at function defintion time.

```javascript
var teacher = {
	name: 'Shane',
	speak: function() {

		//Bind a function to a specific context
		var boundFunction = function(){
			console.log('later my name is ' + this.name);
		}.bind(this);

		//boundFunction will always run in bound context
		setTimeout(boundFunction,1000);
	}
}
```

#### .call() and .apply()
Explicitly set the `this` value at execution time.

```javascript
var teacher = {name: 'Shane'};

var speak = function(item1, item2){
	console.log(this.name, item1, item2);
}

speak.call(teacher, 'coffee', 'ramen'); //'Shane', 'coffee', 'ramen'
speak.apply(teacher, ['coffee', 'ramen']); //'Shane', 'coffee', 'ramen'
```
The only difference is that `.apply()` takes an array of arguments to pass to the function. `.call()` passes arguments one by one.

## Exercise 2: Async programming

Going back to our slideshow object, let's add some functionality.

1. A `play()` function that moves to the next photo ever 2000ms until the end.<br> *Tip - use `intId = setInterval(fn,ms)`*.
2. A `pause()` function that stops the slideshow <br> *Tip - use `cancelInterval(intId)`*
3. Automatically pause the slideshow if it gets to the end of the photolist while playing.

## Exercise 2 Answer
```javascript
var slideshow = {
    photoList: ['birds', 'puppies', 'rainbows', 'kittens', 'babies'],

    currentPhotoIndex: 0,

    nextPhoto: function() {
        if(this.currentPhotoIndex < this.photoList.length - 1) {
            this.currentPhotoIndex++;
            console.log('currentPhoto is: '+ this.photoList[this.currentPhotoIndex]);
        } else {
            console.log('End of Slideshow')
        }
    },

    prevPhoto: function() {
        if(this.currentPhotoIndex > 0) {
            this.currentPhotoIndex--;
            console.log('currentPhoto is: ' + this.photoList[this.currentPhotoIndex]);
        } else {
            console.log('Start of Slideshow')
        }
    },

    getCurrentPhoto: function() {
        return this.photoList[this.currentPhotoIndex];
    },

    playInterval: null,

    play: function() {
        var self = this;
        this.playInterval = setInterval(function(){self.nextPhoto()}, 2000)
    },

    pause: function() {
        clearInterval(this.playInterval);
    }

}

```

## Homework
- Complete the Javascripting module at [NodeSchool](https://github.com/sethvincent/javascripting)
