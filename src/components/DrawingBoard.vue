<script>
export default {
    data:  function() {
        return {
            snap: null,
            points: [],
            circles: [],
            lines: [],
            closed: false,
            tempLine: null,
            strokeWidth: 3
        }
    },
    mounted : function () {   
        this.snap = Snap("#svg"); 
    },
    watch: {
        points: function(val) {
            // test if the last point match the starting point
            let test = (val.length >= 2 && this.compareMatch(val[0],val[val.length-1]));
            if(test) this.closed = true;
        },
        strokeWidth: function(newStroke) {
            // watch the stroke for realtime changes
            this.lines.forEach(function(line) {
                line.attr({
                    strokeWidth: newStroke
                })
            });
            this.circles.forEach(function(circle) {
                circle.animate({r: newStroke}, 500);
            });
        }
    },
    methods: {
        onclick(event) {
            let point = {
                x: event.clientX,
                y: event.clientY
            };
            // if the polygon is closed we do not draw anymore
            if(!this.closed) {
                // save points to comare them later
                this.points.push(point);
                let circle = this.snap.circle(point.x, point.y, this.strokeWidth);
                this.circles.push(circle);
                this.drawLine();
            }
        },
        onMouseMove(event) {
            let point = {
                x: event.clientX,
                y: event.clientY
            };
            if(this.points.length >=1 && !this.closed) {
                let startingPoint = {
                    x: this.points[this.points.length-1].x,
                    y: this.points[this.points.length-1].y
                }
                if(this.tempLine) this.tempLine.remove();
                this.tempLine = this.snap.line(startingPoint.x, startingPoint.y, point.x, point.y).attr({
                    stroke: "#ff0000",
                    strokeWidth: this.strokeWidth
                });
            }
        },
        compareMatch(point1, point2) {
            let xDiff = point2.x - point1.x;
            let yDiff = point2.y - point1.y;
            if(Math.abs(xDiff) <= this.strokeWidth && Math.abs(yDiff)<=this.strokeWidth) {
                return true;
            }
            return false;
        },
        drawLine() {
            let length = this.points.length;
            if(length < 2 || this.closed) return;

            let startingPoint = this.points[length - 2];
            let finishingPoint = this.points[length - 1];

            let line = this.snap.line(startingPoint.x, startingPoint.y, finishingPoint.x, finishingPoint.y).attr({
                stroke: '#00ff00',
                strokeWidth: this.strokeWidth
            });
            this.lines.push(line);
        },
        clear() {
            this.snap.clear();
            this.points = [];
            this.lines = [];
            this.circles = [];
            this.closed = false;
        }
    }
}
</script>

<template>
<div id="wrapper">
    <div class="panel">
        <div class="input">
            <span>Line thikness:</span> 
            <input class="strokeWidth" type="number" min="3" max="10" v-model="strokeWidth">
            <button @click="clear">Clear</button>
        </div>
        <div class="msg">
            <span v-if="points.length == 0">Start by creating a point anywhere you want</span>
            <span v-if="points.length > 0 && !closed">Click to draw the line</span>
            <span v-if="closed">Congragulations you polygon is ready</span>
        </div>
    </div>
    <svg id="svg" @click="onclick" @mousemove="onMouseMove"></svg>
</div>
</template>

<style>
    #wrapper .panel {
        background-color: #42b983;
        color: #fff;
        position: absolute;
        top: 0;
        width: 100%;
        padding: 15px;
        box-sizing: border-box;
        text-align: center
    }
    #wrapper .panel .input {
        margin-bottom: 10px;
    }
    #wrapper .panel .input input,
    #wrapper .panel button{
        padding: 5px;
        width: auto;
        border: 0;
    }
    #wrapper .panel .msg {
        text-align: center
    }
    #wrapper #svg{
        height: 100vh;
        width: 100%;
    }
</style>
