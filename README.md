# test-1
generative code
// You can find the Turtle API reference here: https://turtletoy.net/syntax
Canvas.setpenopacity(0.5);

const size = 90;                //min = 10, max = 100, step = 5
const perlinSize = 3;          //min = 1, max = 50, step = 1
const maxTurtles = 5000;        //min = 100, max = 10000, step = 1

// Global code will be evaluated once.
const turtle = new Turtle();

class Perlin{
    constructor(size, gridSize){
        this.size = size;
        this.gridSize = gridSize;
        
        this.grid = [];
        
        // For each grid intersection, compute a random unit vector
        for(let i = 0; i <= gridSize; i++){
            let table = [];
            for(let j = 0; j <= gridSize; j++){
                
                let angle = Math.random() * 2 * Math.PI;
                let x = Math.cos(angle);
                let y = Math.sin(angle);

                table.push([x, y]);
//                console.log([x, y]);
            }
            this.grid.push(table);
        }
//        console.log(this.grid);
    }
    
    get(x, y){
        x = x / 2 + this.size / 2;
        y = y / 2 + this.size / 2;
        if(x < 0) x = 0;
        if(x >= this.size) x = this.size - 0.01;
        if(y < 0) y = 0;
        if(y >= this.size) y = this.size - 0.01;
        
//        console.log(x, y);
        
        let posx = x * this.gridSize / this.size;
        let posy = y * this.gridSize / this.size;
        
//        console.log(posx, posy);
        
        let x1 = Math.floor(posx);
        let x2 = x1 + 1;
        let y1 = Math.floor(posy);
        let y2 = y1 + 1;

        let scal = [];
        
        scal.push()
        
        scal.push(this.scalar(posx, posy, x1, y1));
        scal.push(this.scalar(posx, posy, x2, y1));
        scal.push(this.scalar(posx, posy, x1, y2));
        scal.push(this.scalar(posx, posy, x2, y2));
        
//        console.log(scal);

        // interpolate : linear interpolation for a start
        let int1 = this.interpolate(posx - x1, scal[0], scal[1]);
        let int2 = this.interpolate(posx - x1, scal[2], scal[3]);

//        console.log(int1, int2);
        
        return this.interpolate(posy - y1, int1, int2);
    }
    
    scalar(x, y, vx, vy){
        x -= vx;
        y -= vy;
//        console.log(x, y);
        return x * this.grid[vx][vy][0] + y * this.grid[vx][vy][1];
    }
    
    smooth(v){
        if(v < 0) v = 0;
        if(v > 1) v = 1;
        return v**2 * (3 - 2*v);

    }
    
    interpolate(x, a, b){
        return a + (b - a) * this.smooth(x)
    }
}

let perlin = new Perlin(size, perlinSize);

let posx = -size;
let posy = -size;

let gen = 0;

// The walk function will be called until it returns false.
function walk(i) {
    let amp = perlin.get(posx, posy)
//    console.log(amp);
    amp = (amp + 1) / 2;
    turtle.seth(amp * 360);
    turtle.forward(0.1);
    
    posx = turtle.x();
    posy = turtle.y();
    
    if(Math.abs(posx) > size || Math.abs(posy) > size){
//        console.log(posx, posy);
        gen++;
        posx = Math.random() * 2*size - size;
        posy = Math.random() * 2*size - size;
        
        turtle.jump(posx, posy);
        turtle.seth(Math.random() * 360);
    }

/*    
    for(let i of perlin.grid){
        for(let j of i){
            console.log(j);
//            console.log(Math.sqrt(j[0]**2 + j[1]**2));
            console.log(j[0]);
            console.log(j[1]);
        }
    }
*/
    return gen < maxTurtles;
}
