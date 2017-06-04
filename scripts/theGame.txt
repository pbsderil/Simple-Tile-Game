var Tile = function(x,y,val,elem){
	this.x =x;
	this.y=y;	
	this.val = val;
	this.elem =elem;
};

var Game = function(X){
	var trigger = null;
	var shuffle =["ArrowDown","ArrowDown","ArrowRight","ArrowRight","ArrowDown","ArrowRight","ArrowUp","ArrowLeft","ArrowUp","ArrowRight","ArrowUp"];
	this.X =X;
	var map ={};
	this.render = function(){
		var rowRef = document.getElementById("row1");
		var tileRef = document.getElementById("tile1");
		var tileClone = tileRef.cloneNode(false);
		
		
		tileClone.textContent = 1;
		tileClone.style.top="0vh";
				tileClone.style.left="0vh";
				tileClone.setAttribute("position","1,1");	
				map["1,1"] = tileClone;
rowRef.innerHTML="";
rowRef.append(tileClone);
				tileClone = tileRef.cloneNode(false);
		var top =0;
		var left =16;
		for(var i =2;i<=this.X*this.X;i++){

				var temp = tileClone;
				temp.setAttribute("id",i);
				var yC = i%this.X==0?this.X:i%this.X;
				var xC = Math.ceil(i/this.X);
			
				if(i!== this.X*this.X){
				temp.textContent = i;}else{
					temp.style.border ="1px solid blue"
					temp.setAttribute("tabindex","0");
					temp.addEventListener("keydown",keyDownHandler);
					trigger = temp;
				}			

				temp.style.top=top+"vh";
				temp.style.left=left+"vh";
				temp.setAttribute("position",xC+","+yC);				
				var tile = new Tile(xC,yC,i,temp);
				map[xC+","+yC] = tileClone;
				rowRef.append(temp);
			tileClone = tileRef.cloneNode(false); 
			left+=16;
			if(i%this.X==0){
				top+=16;
				left=0;
			}
			
	}
	
	for(var i=0;i<shuffle.length;i++){
		var event={};
		event.target = trigger;
		event.key = shuffle[i];
		event.passive = true;
		keyDownHandler(event);
	}
	}
	function keyDownHandler(event){
		if(!event.passive ){
			shuffle.push(event.key);
		}
	var elem = event.target;
	var position = elem.getAttribute("position").split(",");

	if(event.key =="ArrowUp"){
		if(+position[0] <X){
			var elemToMove = map[+position[0]+1+","+position[1]];
			move(elemToMove,elem,"up",position);
		}
	}
	else if(event.key =="ArrowDown"){
		if(+position[0] >1){
			var elemToMove = map[+position[0]-1+","+position[1]];
			move(elemToMove,elem,"down",position);
		}
	}else if(event.key =="ArrowLeft"){
		if(+position[1] <X){
			var elemToMove = map[position[0]+","+(+position[1]+1)];
			move(elemToMove,elem,"left",position);
		}
	}else if(event.key =="ArrowRight"){
		if(+position[1] >1){
			var elemToMove = map[position[0]+","+(position[1]-1)];
			move(elemToMove,elem,"right",position);
		}
	}

	
}
function move(elem ,elemB, direction ,position){
	if(direction =="up"){
		var top = parseInt(elem.style.top);
		i=top-1;
		var inter = setInterval(function(){
			if(i<top-16){
				clearInterval(inter);
			}else{
			elem.style.top = i+"vh";
			elemB.style.top = (parseInt(elemB.style.top)+1)+"vh";
			i--;
		}
		},5);
		
	}else if(direction =="down"){
		var top = parseInt(elem.style.top);
		var i = top+1;
		var inter = setInterval(function(){
			if(i>top+16){
				clearInterval(inter);
			}else{
			elem.style.top = i+"vh";
			elemB.style.top = (parseInt(elemB.style.top)-1 )+"vh";
			i++;
		}
		},5);
		

	}
	if(direction =="left"){
		var left = parseInt(elem.style.left);
		var i = left-1;
		var inter = setInterval(function(){
			if(i<left-16){
				clearInterval(inter);
			}else{
			elem.style.left = i+"vh";
			elemB.style.left = (parseInt(elemB.style.left)+1 )+"vh";
			i--;
		}
		},5);
		

	}
	if(direction =="right"){
		var left = parseInt(elem.style.left);
		var i = left+1;
		var inter = setInterval(function(){
			if(i>left+16){
				clearInterval(inter);
			}else{
			elem.style.left = i+"vh";
			elemB.style.left = (parseInt(elemB.style.left)-1 )+"vh";
			i++;
		}
		},5);
		

	}
	
	/*var tempX =tileA.x;
	var tempY = tileA.y;
	tileA.x = tileB.x;
	tileA.y = tileB.y;
	tileB.x = tempX;
	tileB.y = tempY;*/
	var p =elem.getAttribute("position");
	elem.setAttribute("position",elemB.getAttribute("position"));
	elemB.setAttribute("position",p);
	var tileA = map[elem.getAttribute("position")];
	var tileB = map[elemB.getAttribute("position")];
	map[elemB.getAttribute("position")] = tileA;
	map[elem.getAttribute("position")] = tileB;
	if(validate(X) == true){
		document.getElementById("successMsg").style.display="block";
		trigger.removeEventListener("keydown",keyDownHandler);
	}
};  

function validate (X){
	var tiles = document.getElementById('row1').children;
for(var i=0;i<tiles.length-1;i++){
	var position = tiles[i].getAttribute("position").split(",");
	if((position[0]-1)*X+(+position[1]) != +(tiles[i].textContent)){
		return false;
	}
}
return true;
};

this.solve = function (){
	var i = shuffle.length-1;

	var inter = setInterval(function(){
		if(i<0){
			clearInterval(inter);
		}else{
			var event ={};
			event.target = trigger;
			event.passive = true;

		if(shuffle[i]=="ArrowRight"){
			event.key ="ArrowLeft";
			keyDownHandler(event);
		}
		if(shuffle[i]=="ArrowLeft"){
			event.key ="ArrowRight";
			keyDownHandler(event);
		}
		if(shuffle[i]=="ArrowDown"){
			event.key ="ArrowUp";
			keyDownHandler(event);
		}
		if(shuffle[i]=="ArrowUp"){
			event.key ="ArrowDown";
			keyDownHandler(event);
		}
		}
		i--;
	},700);
	
}
};


var game = new Game(4);
game.render();

function solve(){
	game.solve();
}
