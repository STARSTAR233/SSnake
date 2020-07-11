# SSnake
🐍
      //① 绘制地图
      function Map() {
          //私有成员(不会随便发生变化)
          var w = 800;
          var h = 400;
          //成员方法，绘制地图
          this.showmap = function () {
              //创建div、设置css样式、追加给body
              var tu = document.createElement('div');
              tu.style.width = w + "px";
              tu.style.height = h + "px";
              tu.style.backgroundImage = "url(./12.jpg)";
              document.body.appendChild(tu);
          }
      }
      //② 绘制食物
      function Food() {
          var len = 20;
          //把食物(权值)坐标声明为公开的，以便在外部访问
          this.xFood = 0;
          this.yFood = 0;
          this.piece = null; //页面上唯一的食物对象
          //绘制
          this.showfood = function () {
              //创建div、设置css样式、追加给body
              if (this.piece === null) {
                  this.piece = document.createElement('div');
                  this.piece.style.width = this.piece.style.height = len + "px";
                  this.piece.style.backgroundColor = "green";
                  this.piece.style.position = "absolute";
                  document.body.appendChild(this.piece);
              }
              //食物设置绝对定位(position/left/top)
              //食物位置“随机”摆放
              //移动步进值：20px
              //食物“权值”坐标： X轴(0-39)  Y轴(0-19)
              //食物真实坐标：权值坐标 *  步进值
              this.xFood = Math.floor(Math.random() * 40);  //0-39的随机数
              this.yFood = Math.floor(Math.random() * 20);  //0-19的随机数
              this.piece.style.left = this.xFood * len + "px";
              this.piece.style.top = this.yFood * len + "px";
          }
      }
      //③ 小蛇
      function Snake() {
          var len = 20;
          this.redirect = "right"; //默认向右边移动
          //后期snakebody要变化，因此声明为公开的(每个蛇节：[x坐标，y坐标，颜色，蛇节对象])
          this.snakebody = [[0, 1, 'green', null], [1, 1, 'green', null], [2, 1, 'green', null], [3, 1, 'red', null]];
          //a.绘制小蛇
          this.showsnake = function () {
              //遍历小蛇的各个蛇节，并依次创建即可
              for (var i = 0; i < this.snakebody.length; i++) {
                  //this.snakebody[i]//代表每个蛇节
                  //创建蛇节div
                  if (this.snakebody[i][3] === null) {//判断没有创建对应的蛇节
                      this.snakebody[i][3] = document.createElement('div');
                      //设置css样式(宽度、高度、颜色)
                      this.snakebody[i][3].style.width = this.snakebody[i][3].style.height = len + "px";
                      this.snakebody[i][3].style.backgroundColor = this.snakebody[i][2];
                      //绝对定位及位置
                      this.snakebody[i][3].style.position = "absolute";
                      //把蛇节追加给body
                      document.body.appendChild(this.snakebody[i][3]);
                  }
                  this.snakebody[i][3].style.left = this.snakebody[i][0] * len + "px";
                  this.snakebody[i][3].style.top = this.snakebody[i][1] * len + "px";
              }
          }
          //b.移动小蛇
          this.movesnake = function () {
              //非蛇头蛇节(当前蛇节的新坐标 是"下个蛇节"的旧坐标)
              for (var i = 0; i < this.snakebody.length - 1; i++) {
                  this.snakebody[i][0] = this.snakebody[i + 1][0];
                  this.snakebody[i][1] = this.snakebody[i + 1][1];
              }
              if (this.redirect == "right") {
                  //蛇头x坐标递增
                  this.snakebody[this.snakebody.length - 1][0] += 1;
              }
              if (this.redirect == "left") {
                  //蛇头x坐标递减
                  this.snakebody[this.snakebody.length - 1][0] -= 1;
              }
              if (this.redirect == "up") {
                  //蛇头y坐标递减
                  this.snakebody[this.snakebody.length - 1][1] -= 1;
              }
              if (this.redirect == "down") {
                  //蛇头y坐标递增
                  this.snakebody[this.snakebody.length - 1][1] += 1;
              }
              //判断蛇头碰到食物
              //蛇头坐标
              var xSnake = this.snakebody[this.snakebody.length - 1][0];
              var ySnake = this.snakebody[this.snakebody.length - 1][1];
              //食物坐标food.xFood/food.yFood;
              if (xSnake == food.xFood && ySnake == food.yFood) {
                  //吃食物增加蛇节
                  var newjie = [this.snakebody[0][0], this.snakebody[0][1], 'green', null];
                  this.snakebody.unshift(newjie);//把newjie放到数组的第一个位置去
                  //原食物消失，重新生成一个食物
                  food.showfood();
              }
              //控制小蛇在地图范围内移动
              if (xSnake < 0 || xSnake > 39 || ySnake < 0 || ySnake > 19) {
                  alert('game over');
                  clearInterval(mytime);
                  return false;
              }
              //吃到自己判断(蛇头坐标与其他蛇节坐标一致)
              for (var k = 0; k < this.snakebody.length - 1; k++) {
                  if (this.snakebody[k][0] == xSnake && this.snakebody[k][1] == ySnake) {
                      alert('game over kill you by yourself');
                      clearInterval(mytime);
                      return false;
                  }
              }
              //根据新坐标绘制小蛇
              this.showsnake();
          }
      }
      window.onload = function () {
          var map = new Map();
          map.showmap();
          food = new Food();//声明为全局的以便在该加载事件函数外部访问
          food.showfood();
          snake = new Snake();//声明为全局的snake对象
          snake.showsnake();
          //移动小蛇
          //setInterval(全局变量，时间)
          mytime = setInterval("snake.movesnake()", 200);
          //设置键盘事件，控制器小蛇移动方向
          document.onkeydown = function (evt) {
              var num = evt.keyCode;//通过事件对象获得数值码，进而知道被触发键子
              if (num == 38) {
                  snake.redirect = "up";
              }
              if (num == 40) {
                  snake.redirect = "down";
              }
              if (num == 37) {
                  snake.redirect = "left";
              }
              if (num == 39) {
                  snake.redirect = "right";
              }
          }
      }
