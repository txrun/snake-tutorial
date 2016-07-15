# snake-tutorial



Its been a only a couple of months since I started learning JavaScript, and one of the expert tip I got was to try, tackle and solve the classic snake game. For someone who has had no experience in programming the initial thought of dealing with the problem was a rush regarding the approach I should take - from the point from where I should start solving it, the data structure I should be using, or how I will even get to display the graphics, how the snake should be represented,etc. For this reason, from this point in on I'll call it the snake problem rather than the snake game.


###The Canvas###
A simple Google search led me to discovering the html element <canvas> which is a useful for displaying graphics and animations in the browser. So at this point I had a tool for my game screen, this definite amount of space where my game should happen.


A declaration for Canvas dimensions 800x400 and border styling is as shown:

```
	<canvas id ="myCanvas" width="800" height="400" style="border:1px solid #111; border-radius:5px"></canvas>	

```

This will render the game space, of dimensions 800x400 with appropriate border styling, in your browser window(by default a canvas has no border or content)- 

![blank canvas](/home/user1/Downloads/index2.html.jpg)

<screenshot>


The different functionalities of the game will be encapsulated into the different functions. These include: 
* Drawing an initial snake of default length
* Rendering this snake
* Make the snake move across the canvas along the x and y axes
* Generating the food element
* The snake growing in length having eaten the food
* Keep a tab of this by recording a score, finally detect a collision if the snake runs into itself or a wall.

Before anything, the first thing is to decide how the snake should be represented. For this game, the snake game is displayed in the form of a string of square cells - each square represents a food item eaten by the snake.

Here we have a default length of 5 for the snake. Length of the snake = no. of squares in the snake.

In order to draw graphics on the canvas using JavaScript, we need an object of the getContext() method.

```
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
```

###The default Snake###
So, the first thing we can do is draw a stationary snake; a snake at the starting position, the starting position being the top left corner. Three methods are defined for this purpose - initialSnakeCoordinates(), paintCell(), and drawSnake().

initialSnakeCoordinates() populates an array pos[] with the x and y coordinates for default position of the initial length of cells. paintCell() is used to paint a single cell. drawSnake() calls the method paintCell() for each unit length of the snake.
The code at this point  - 

```
function initialSnakeCoordinates()
{
	var initLength = 5;
	var pos = [];
	for(var i=0;i<initLength;i++)
	{
		pos.push({x:i,y:0});
	}
	return pos;
}

function paintCell(x,y)
{
	ctx.fillStyle="blue";
	ctx.fillRect(x*cw,y*cw,cw,cw);
	ctx.strokeStyle="white";
	ctx.strokeRect(x*cw,y*cw,cw,cw);

}

function drawSnake()
{
	ctx.clearRect(0,0,canvas.width,canvas.height);
	for (var i=snakeArray.length -1;i>=0;i--)
	{		
		paintCell(snakeArray[i].x,snakeArray[i].y)
	}
}

</html>
```

At this point we have a snake of length five that sits motionless at the top left corner of the canvas.

<screenshot>


####The Game Loop####
An important aspect while implementing any game is the game loop. A game loop pumps the game forward. Here, there is a function which implements the game loop called gameLoop(). gameLoop() renders and initialises the snake, renders the snake after each step it has taken, renders the food and displays the score - all this done as long as the value of the global boolean variable isGameLive is ‘true’. The variable isGameLive comes in handy when game needs to exit from the game loop when the snake dies from collision.
The gameLoop() is called at regular intervals using the setInterval() method. The drawSnake() method is moved into the gameLoop().


###Move the snake along the X & Y axes###
Next we have to animate the snake - make it move along the two axes of the canvas using keystrokes for the different directions. Here we will be using the WASD keys to make the snake move up, left, down, and right along the canvas.

When the game starts, by default the snake should be moving right. For this purpose we have a global variable currentDirection assigned with the value "right". Now for each value of currentDirection(left, right, up or down) we need respective conditions so the snake takes up or follows that direction. These conditions for direction of movement of the snake is written in the function makeNextMove(). The change in direction is employed using the position of the head of the snake which is obtained using the snakeHead() method. 

```
function snakeHead(snakeArray)
{
  var headPos = snakeArray.length - 1;
  return snakeArray[headPos];
}


function makeNextMove()
{
  var head = snakeHead(snakeArray);


  if(currentDirection == "right")

        snakeArray.push({x: head.x + 1,y: head.y}); 

  else if(currentDirection == "left")

      snakeArray.push({x:head.x-1,y:head.y});
  
  else if(currentDirection == "up")

      snakeArray.push({x:head.x,y:head.y-1});

  else if (currentDirection == "down")

      snakeArray.push({x:head.x,y:head.y+1});

}
```

Also, the value for currentDirection is determined by making use of the onkeypress event handler.

```
document.onkeypress = function (event)
{
	if (event.charCode == '97' && currentDirection != "right")
	{
		currentDirection = "left";
	}
	else if (event.charCode=='100' && currentDirection != "left")
	{
		currentDirection="right";
	}
	else if (event.charCode=='115' && currentDirection != "up")
	{
		currentDirection="down";
	}
	else if (event.charCode=='119' && currentDirection != "down")
	{
		currentDirection="up";
	}
	else if(event.charCode=='112')	// p
	{																
		isGameLive=!isGameLive;				// pause
	}
}
```

At this point the output will be something like this - 

<screenshot of snake trail>

The snake is leaving behind a trail! To solve this simply use the splice() and delete a cell from the back of the snake. Now we have a snake of length 5 units capable of moving across the canvas.
<screenshots 3nos>

###Generate food for the snake###
The next step is to generate the food item at a random location for the snake to grow in length during the game. The x and y coordinates for the position of the food is randomly generated using the Math.random() , and since the values have a upper and lower bound (the dimensions of the canvas) the values are kept in range using the Math.floor(). The food item is rendered in the same size of a single cell of the snake using the methods newFoodCoordinates() and renderFood().

```
function newFoodCoordinates()
{
  var x1=Math.floor(((Math.random()*790)+0)/10)*10;
  var y1=Math.floor(((Math.random()*390)+0)/10)*10;
  
  return {x:x1,y:y1};
}

var food = newFoodCoordinates();

function renderFood()
{
  var x =food.x;
  var y =food.y;
  ctx.fillStyle = "red";
  ctx.fillRect(x,y,cw,cw);  
}
```

And call the renderFood() method in the gameLoop().

Now that we have a snake of minimum length and a food item being spawned at random location in the canvas , the next logical step is to facilitate the snake to eat the cake and simultaneously keep a tab of it by defining a method scoreCard(). To let the snake eat the food we define a method eatFood() which checks to see if the coordinates of the snake’s head  matches with that of the spawned food. If it does eatFood() returns a true value or else a false. The eatFood() method is invoked inside makeNextMove() where, if the snake eats food the score is recorded and newFoodCoordinates() is invoked to spawn food at the next random location in the canvas, and if the food is not eaten the splice() method is called to keep the length of the snake same as before.

```
function newFoodCoordinates()
{
  var x1=Math.floor(((Math.random()*790)+0)/10)*10;
  var y1=Math.floor(((Math.random()*390)+0)/10)*10;
  return {x:x1,y:y1};
}

function renderFood()
{
  var x =food.x;
  var y =food.y;
  ctx.fillStyle = "red";
  ctx.fillRect(x,y,cw,cw);  
}


function eatFood()
{
  var head = snakeHead(snakeArray);
  var x=head.x;
  var y=head.y;

  if(x*cw ==food.x && y*cw==food.y)
  {
    return true;
  }
  else
    return false;
}

function scoreCard()
{
  ctx.fillStyle = "black";
  ctx.font="14px Arial";
  ctx.fillText("Score :" + s + "w:top a:left s:down d:right",10,395);
}


```

Finally, when is “Game Over!!” ?? For our basic snake game we are not introducing any obstacles in the path of the snake. So, the only scenario when the snake will die is when it collides on itself. What I have done here is create a temp variable tempArray which has the contents of the snakeArray i.e. the coordinate pair values for each cell of the snake. Based on the direction of the snake a condition is formulated to see whether the coordinates of the head of the snake matches with that of any of the values in the tempArray. If it matches it means the snake has collided with itself and we exit from the game.
If the snake is moving to the right at present the code snippet will look like this - 

```
if(currentDirection == "right")
	{
		for (var i = 0; i < tempArray.length; i++) 
		{	
			if(head.x+1 == tempArray[i].x && head.y == tempArray[i].y)
			{
				isGameLive = false;
				alert("Game Over!! \n Score :"+s);
				console.log("collision!");
			}
		};
	
	}
```

Similarly, for the other 3 directions the only difference will be in the if-condition : 
When the snake is moving left -

```
if(head.x-1 == tempArray[i].x && head.y == tempArray[i].y)
{
.
.
:
}
```

And when moving up - 

```
if(head.y-1 == tempArray[i].y && head.x == tempArray[i].x)
{
.
.
:
}
```

And when moving down - 

```
if(head.y+1 == tempArray[i].y && head.x == tempArray[i].x)
{
.
.
:
}
```

Now, our game has a snake moving around and growing in length when it eats food that is being generated at random location, simultaneously keeping a record of the score, and finally dies if it collides into itself.

Right now the only loose end is as to what should happen when the snake reaches the limits of the canvas? We have two options - it can either die or wrap around to the opposite edge. Here, I am sticking with the latter and let the snake wrap around.
So, to wrap around, at the point where the snakeArray.push() is being invoked we check if the position of the head of the snake has reached the limits of the canvas, and when it does reinitialise the values of the coordinates of the head with that of the opposite edge of the canvas.

For example, if the snake has reached the right edge of the canvas if-condition will be 

```
if(head.x>79)
	{
		newHead.x=0;
		snakeArray.push({x:newHead.x+1,y:newHead.y});
	}		
	else
	{
		snakeArray.push({x: head.x + 1,y: head.y});
	}
```

Ok, to clear up two things - why the number 79?, and why only one of the coordinate value pair is checked? Well the answer to first question - remember we use cells of size 10? So if the head.x has value 79 it means it is at position 79x10 = 790 of the canvas, and the next value of head.x =80 is at 800th pixel of the canvas(remember our canvas size is 800x400?). The answer to the second question as to why only a single value of the coordinate pair is checked - simple mathematics, or in this case geometry! The snake is moving across the horizontal axis here which means y-value of the coordinate pair remains same. Similarly, for when the snake is moving across the vertical axes x-value of the coordinate pair remains the same. So, the code for the snake to wrap around while it moves in the up direction will be - 

```
if(head.y<0)
		{
			newHead.y=40;
			snakeArray.push({x:newHead.x,y:newHead.y-1});
		}		
		else
			snakeArray.push({x:head.x,y:head.y-1});
```

Now, we have a snake game with its set of basic functionalities.

Here is the code that has evolved in the process - 

```
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <canvas id ="myCanvas" width="800" height="400" style="border:1px solid #111; border-radius:5px"></canvas>  
</body>

<script>


var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");

var snakeArray=initialSnakeCoordinates();
var currentDirection="right";
var isGameLive=true;


function initialSnakeCoordinates()
{
  var initLength = 5;
  var pos = [];
  for(var i=0;i<initLength;i++)
  {
    pos.push({x:i,y:0});
  }
  return pos;
}


// paint each cell of the snake

function paintCell(x,y)
{
  ctx.fillStyle="blue";
  ctx.fillRect(x*cw,y*cw,cw,cw);
  ctx.strokeStyle="white";
  ctx.strokeRect(x*cw,y*cw,cw,cw);

}

function drawSnake()
{
  ctx.clearRect(0,0,canvas.width,canvas.height);

  for (var i=snakeArray.length -1;i>=0;i--)
  {   
    paintCell(snakeArray[i].x,snakeArray[i].y)
  }
}

function snakeHead(snakeArray)
{
  var headPos = snakeArray.length - 1;
  return snakeArray[headPos];
}

var s=0; // to keep tab of the score

function makeNextMove()
{
  var head = snakeHead(snakeArray);
  
  var newHead = snakeHead(snakeArray);
  var tempArray = snakeArray;

if(currentDirection == "right")
  {
    for (var i = 0; i < tempArray.length; i++) 
    { 
      if(head.x+1 == tempArray[i].x && head.y == tempArray[i].y)
      {
        isGameLive = false;
        alert("Game Over!! \n Score :"+s);
        console.log("collision!");
      }
    };
  
    
    if(head.x>79)
    {
      newHead.x=0;
      snakeArray.push({x:newHead.x+1,y:newHead.y});
    }   
    else
      {
        snakeArray.push({x: head.x + 1,y: head.y});
        
      }
      
  }

  else if(currentDirection == "left")
  { 
    for (var i = 0; i < tempArray.length; i++) 
    { 
      if(head.x-1 == tempArray[i].x && head.y == tempArray[i].y)
      {
        isGameLive = false;
        alert("Game Over!! \n Score :"+s);
        console.log("collision!");
      }
    };

    if(head.x<0)
    {
      newHead.x = 79;
      snakeArray.push({x:newHead.x-1,y:newHead.y}); 
    }
    else
      snakeArray.push({x:head.x-1,y:head.y});

      
  }
  
  else if(currentDirection == "up")
  {
    for (var i = 0; i < tempArray.length; i++) 
    { 
      if(head.y-1 == tempArray[i].y && head.x == tempArray[i].x)
      {
        isGameLive = false;
        alert("Game Over!! \n Score :"+s);
        console.log("collision!");
      }
    };

    if(head.y<0)
    {
      newHead.y=40;
      snakeArray.push({x:newHead.x,y:newHead.y-1});
    }   
    else
      snakeArray.push({x:head.x,y:head.y-1});


  }
  
  else if (currentDirection == "down")
  {
    for (var i = 0; i < tempArray.length; i++) 
    { 
      if(head.y+1 == tempArray[i].y && head.x == tempArray[i].x)
      {
        isGameLive = false;
        alert("Game Over!! \n Score :"+s);
        console.log("collision!");
      }
    };

    if(head.y>39)
    {
      newHead.y=0;
      snakeArray.push({x:newHead.x,y:newHead.y+1});
    }   
    else
      snakeArray.push({x:head.x,y:head.y+1});


      
  }

  if(!eatFood())
  {
    snakeArray.splice(0,1);
  }
  else
  {
    food = newFoodCoordinates();
    s=s+1;
    return s;
  }
  
}

document.onkeypress = function (event)
{
  if (event.charCode == '97' && currentDirection != "right")
  {
    currentDirection = "left";
  }
  else if (event.charCode=='100' && currentDirection != "left")
  {
    currentDirection="right";
  }
  else if (event.charCode=='115' && currentDirection != "up")
  {
    currentDirection="down";
  }
  else if (event.charCode=='119' && currentDirection != "down")
  {
    currentDirection="up";
  }
  else if(event.charCode=='112')  // p
  {                               
    isGameLive=!isGameLive;       // pause
  }
}


function newFoodCoordinates()
{
  var x1=Math.floor(((Math.random()*790)+0)/10)*10;
  var y1=Math.floor(((Math.random()*390)+0)/10)*10;
  
  return {x:x1,y:y1};
}

var food = newFoodCoordinates();

function renderFood()
{
  var x =food.x;
  var y =food.y;
  ctx.fillStyle = "red";
  ctx.fillRect(x,y,cw,cw);  
}


function eatFood()
{
  var head = snakeHead(snakeArray);

  var x=head.x;
  var y=head.y;

  if(x*cw ==food.x && y*cw==food.y)
  {
    return true;
  }
  else
    return false;
}

function scoreCard()
{
  
  ctx.fillStyle = "black";
  ctx.font="14px Arial";
  ctx.fillText("Score :" + s + "w:top a:left s:down d:right",10,395);

}


function gameLoop()
{
  if(isGameLive)
  {
    drawSnake();
    makeNextMove();
    renderFood();
    scoreCard();
  }
}

setInterval(gameLoop,150);

</script>

</html>
```

So finally, we no longer have a snake problem, but we do have a snake game!

