# S.O.L.I.D.

> S.O.L.I.D. is a set of guidelines for object-oriented programming that was introduced by [Robert C. Marten](https://en.wikipedia.org/wiki/Robert_C._Martin "A.K.A. Uncle bob") around 1995 in response to some issues he saw in Object Oriented code

## What does it fix?

- **RIGIDITY:**

  A change in the code might affect other parts of the code
  
- **FRAGILITY:**

  A change might lead to things breaking in unrelated places
  
- **IMMOBILITY:**

  Not being able to use the code outside of it's original context

## It's an abbreviation for the following:

- [**" S "**  Single responsibility principle](#single-responsibility-principle-)
- [**" O "**  Open closed principle](#open-closed-principle-)
- [**" L "**  Liskov substitution principle](#liskov-substitution-principle-)
- [**" I "**  Interface segregation principle](#interface-segregation-principle-)
- [**" D "**  Dependency inversion principle](#dependency-inversion-principle-)

### Single responsibility principle :

> A Class should have only one reason to change

```js
// We have those two classes "Circle" and "Square"

function Circle(raduis){
  this.raduis = raduis;
}

function Square(length){
  this.length = length;
}

// And we have another  Class that has method "sum"

AreaCalculator.prototype.sum = function(shapes){

  // returns the sum of all areas of the shapes provided
}

// Now we want to return some value to the user

//Does this function belong here "In the AreaCalculator class"???
AreaCalculator.prototype.output = function(){
  return `The sum of all shapes is ${this.sum()}.`
}

//NO

//What we should do is reformat this code and make a class that is responsible for handling the return

function CalculatorOutput(areas){
  this.areas = areas;
}

CalculatorOutput.prototype.good = function(){
  return `The Area of the shapes is ${this.areas.sum()}`;
}

CalculatorOutput.prototype.evil = function(){
  return 'I blocked the output Muahahaha';
}

```


```js


/*
So what we now understand is that the class should only be responsible for only one function
and if we find our selves over-complicating a class or making it do more than it should.

We must refactor our code to make it cleaner.
*/

var circle = new Circle();
var square = new Square();
var shapesArray = [circle, square];

var areas = AreaCalculator.sum(shapesArray)
var output = CalculatorOutput(areas)

output.good();
output.evil();

// We can now add more output styles without altering the AreaCalculator Class

```



### Open closed principle :

> You should be able to extend a class without modifying it

Let us revisit the sum method in the array calculator

```js

// How many times have you found your self using long if..else statements

// Does this logic belong to the area calculator ??? well, it probably doesn't
AreaCalculator.prototype.sum = function(shapes){
  var areaArray = shapes.map(shape => {
    if(shape instanceof Square){
      return Math.pow(shape.length, 2);
    }
    else if(shape instanceof Circle){
      return Math.PI * Math.pow(shape.raduis, 2);
    }
  });
  
  return areaArray.reduce((acc, current) => acc + current)
}
```
What we can do is refactor the code so that each shape has it's own area function

```js
Square.prototype.area = function(){
  return Math.pow(this.length, 2);
}

Circle.prototype.area = function(){
  return Math.PI * Math.pow(this.raduis, 2);
}
```
Now as long as it follows the shape interface our "sum" function should look like this

```js

AreaCalculator.prototype.sum = function(shapes){
  var areaArray = shapes.map(shape => {
    return shape.area();
  });
  
  return areaArray.reduce((acc, current) => acc + current)
}
```

Now we can add more shapes extinding the use of the area calculator whoithout changing any of the code.


### Liskov substitution principle :

> Drived classes must be substituable for their base classes

```js
// Let us assume we have a Rectangle Class

function Rectangle(width, height){
  this.width = width;
  this.height = height;
}

Rectangle.prototype.area = function(){
  return this.width * this.height;
}

```

Now you want to make a square class and you know it's basically a rectangle right.

```js

function Square(length){
  Rectangle.call(this, length, length);
}

Square.prototype = Object.create(Rectangle.prototype);

```

After a while you want to make a diffrent type of a rectangle and since you know that **Square** inherits from **Reactangle** you decied to inherit from **Square**

```js
function BetterRectangle(width, height){
  Square.call(this, width, height);
}
BetterRectangle.prototype = Object.create(Square.prototype);

var newRectangle = new BetterRectangle(2, 3);

newRectangle.area();// RETURNS 4 instead of 6

```
So what happen here is it's behaving like a **Square** would which is not what we wanted

The solution to this is just to make **Square** it's own class


### Interface segregation principle :

> Make fine-grained interfaces with specific methods

To understand this lets look at the following example

```js

function Shape() {};

Shape.prototype.area = function() {/* ... */}
Shape.prototype.volume = function() {/* ... */}

function Triangle(base, height){
  this.base = base;
  this.height - height;
}

Triangle.prototype = Object.create(Shape.prototype);

```

Did you notice what is wrong...

Triangle is a 2D shape so it has no volume so we shouldn't just keep the function like that what we should do is
split the area and the volume into two seperated classes to differentiate between the 2D shapes and 3D ones

```js
function 2DShape() {};
2DShape.prototype.area = function() {/* ... */}


function 3DShape() {};
3DShape.prototype.volume = function() {/* ... */}

var Triangle = new 2DShape();

var Cube = new 3DShape();

```



### Dependency inversion principle :

> Depend on abstractions, not on cocretions

Assume that we have a Picture class

```js
function Picture(color, bg){
  this.Shape = new Shape();
  this.color = color;
  this.background = bg;
}

```

As you can see we made a new object of **Shape** inside **Picture**

making **Picture** totaly dependant on **Shape**

take this as a general rule if you are making a new instance of some other constructor inside the defention of your constructor then you should consider refactoring.

A solution for this is to pass what you need as a parameter

```js
function Picture(shape, color, bg){
  this.Shape = shape;
  this.color = color;
  this.background = bg;
}

var square = new Square(3);

var picture = new Picture(square, "red", "blue");

```


### Refrences

- [A talk about S.O.L.I.D.](https://www.youtube.com/watch?v=GtZtQ2VFweA)
- [Uncle bob talk about S.O.L.I.D.](https://www.youtube.com/watch?v=TMuno5RZNeE)
