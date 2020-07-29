<!-- ============================================================== -->
<!-- b00014 批改作业的弹框组件 -->
<!-- ============================================================== -->
<template>
    <div style="height: 100%;">
        <div class="canvas_container">
            <!--canvas画布-->
            <div class="canvas_main" @scroll.passive="getScroll($event)">
                <!--批改文字-->
                <div v-show="lineType === 'inputText'" :style="'z-index: 199;width: ' + width + 'px;height: ' + height + 'px;position: absolute;'" @mousedown.self="write($event)">
                    <textarea :style="{'margin-left': tranLeft + 'px', 'margin-top': tranTop + 'px', 'max-width': (width-tranLeft)+'px', 'max-height': (height-tranTop)+'px', 'height': tranHeight +'px'}" id="inputText" style="border: 1px dashed rgb(82,172,13);" v-show="isShowWrite" @keydown="changeHeight($event)" v-model="inputText"></textarea>
                </div>

                <canvas :id="radom2" class="student_canvas" :width="width" :height="height"></canvas>
                <canvas
                        :id="radom1"
                        class="teacher_canvas"
                        :class="{canDraw: 'canvas'}"
                        :width="width"
                        :height="height"
                        @mousedown="canvasDown($event, 'mouse')"
                        @mouseup="canvasUp($event, 'mouse')"
                        @mousemove="canvasMove($event, 'mouse')"
                        @touchstart="canvasDown($event, 'touch')"
                        @touchend="canvasUp($event, 'touch')"
                        @touchmove="canvasMove($event, 'touch')">
                </canvas>
            </div>
            <!--批改工具-->
            <div class="ml-2" style="position: relative;">
                <button type="button" class="btn waves-effect waves-light" style="display: block;" @click="isPopover = !isPopover">
                    <i class="mdi mdi-format-color-text"></i>
                </button>
                <div v-if="isPopover" class="popover fade show bs-popover-right" role="tooltip" id="popover156460" style="position: absolute; top: -35px; left: 50px;" x-placement="right">
                    <div class="arrow" style="top: 53px;"></div>
                    <div class="popover-body">
                        <span style="background: #f60404;width: 100px; height: 25px;" @click="lineColor = '#ff0000'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #eebb07;width: 100px; height: 25px;" @click="lineColor = '#eebb07'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #0f0f0f;width: 100px; height: 25px;" @click="lineColor = '#0f0f0f'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #f0f0f0;width: 100px; height: 25px;" @click="lineColor = '#f0f0f0'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #00ca6d;width: 100px; height: 25px;" @click="lineColor = '#00ca6d'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #6c3dec;width: 100px; height: 25px;" @click="lineColor = '#6c3dec'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #0d6aad;width: 100px; height: 25px;" @click="lineColor = '#0d6aad'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                        <span style="background: #f16b05;width: 100px; height: 25px;" @click="lineColor = '#f16b05'">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
                    </div>
                </div>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'line'}" @click="lineType = 'line'"><i class="mdi mdi-ray-start-end"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'circle'}"  @click="lineType = 'circle'"><i class="mdi mdi-panorama-fisheye"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'rec'}"  @click="lineType = 'rec'"><i class="mdi mdi-panorama-wide-angle"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'inputText'}"  @click="lineType = 'inputText'"><i class="mdi mdi-format-text"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'tick'}"  @click="lineType = 'tick'"><i class="mdi mdi-check"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'fork'}"  @click="lineType = 'fork'"><i class="mdi mdi-close"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;height: 33px;width: 40px;"
                        :class="{'btn-info': lineType === 'halfTick'}"  @click="lineType = 'halfTick'">
                    <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAALCAMAAAB4W0xQAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyJpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMy1jMDExIDY2LjE0NTY2MSwgMjAxMi8wMi8wNi0xNDo1NjoyNyAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNiAoV2luZG93cykiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6REY5MjkyQ0JCMTQyMTFFQTgxM0Q4NDkxQjgxNjRBMjAiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6REY5MjkyQ0NCMTQyMTFFQTgxM0Q4NDkxQjgxNjRBMjAiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDpERjkyOTJDOUIxNDIxMUVBODEzRDg0OTFCODE2NEEyMCIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpERjkyOTJDQUIxNDIxMUVBODEzRDg0OTFCODE2NEEyMCIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PlqaXZoAAAASUExURQoKCgYGBhQUFAICAgAAAP///4pqoVkAAAAGdFJOU///////ALO/pL8AAAA8SURBVHjaYmBFAQy4uUwILgsQMSPJsoB4QC4zhMvIAtHLDFYAlGOBGMUM1QczmZkFxoMYxcyMwxkAAQYA7E4C1jV0TLUAAAAASUVORK5CYII=">
                </button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'font' && fontText === '优'}"  @click="lineType = 'font'; fontText = '优'">优</button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'font' && fontText === '错'}"  @click="lineType = 'font'; fontText = '错'">错</button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'font' && fontText === '疑'}"  @click="lineType = 'font'; fontText = '疑'">疑</button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'rubber'}"  @click="lineType = 'rubber'"><i class="mdi mdi-eraser"></i></button>
                <button type="button" class="btn waves-effect waves-light" style="display: block;margin-top: 5px;"
                        :class="{'btn-info': lineType === 'clean'}"  @click="clean"><!--<i class="mdi mdi-refresh"></i>-->清</button>
                <button type="button" class="btn waves-effect waves-light  btn-info" style="display: block;margin-top: 5px;"
                        @click="magnify()"><i class="mdi mdi-magnify-plus"></i></button>
                <button type="button" class="btn waves-effect waves-light btn-info" style="display: block;margin-top: 5px;"
                        @click="narrow()"><i class="mdi mdi-magnify-minus"></i></button>
            </div>
        </div>
    </div>

</template>

<script>

	import uuidv1 from 'uuid/v1';
	export default {
		components:{

		},
		props: {
			answerUrl: {
				type: String,
			},
			teacherUrl: {
				type: String,
			},
		},
		watch: {
			answerUrl: function () {
				this.backgroundCanvas();
				this.initDraw();
			},
			info (val) {
				if (val) {
					this.initDraw()
				}
			}
		},
		data () {
			return {
				canDraw: true,
				lineColor: "#ff0000", // 画笔颜色
				lineWidth: 2,   // 画笔宽度
				isPopover: false,
				// 同一页面多次渲染时，用于区分元素的id
				radom1: uuidv1(),
				radom2: uuidv1(),
				// canvas对象
				context1: {},
				context2: {},
				// 是否处于绘制状态
				canvasMoveUse: false,
				// 画笔类型
				lineType: 'line',
				// 绘制矩形和椭圆时用来保存起始点信息
				beginRec: {
					x: '',
					y: '',
					imageData: ''
				},
				// 储存坐标信息
				drawInfo: [],
				startTouch: null,
				moveTouch: 0,
				width: "612",
				height: "866",
				oriWidth: "612",
				oriHeight: "866",
				// 背景图片缓存
				img: new Image(),
				fontText: '',
				// 文本框显示
				isShowWrite: false,
				tranLeft: 0, // 向左偏移
				tranTop: 0,  // 向右偏移
				tranHeight: 60,
				inputText: '',
				// 放大倍数
				scale: 1,
				// 原canvas
				canvasUrl1: null,
				canvasUrl2: null,
				scrollTop: 0,
				scrollLeft: 0,
			}
		},
		mounted () {
			// 获取屏幕DPI
			let dpi = this.conversion_getDPI()[0];

			let wmm = 210;
			let hmm = 297;

			// 根据dpi得到宽高
			this.width = wmm / 25.4 * dpi/1.5;
			this.height = hmm / 25.4 * dpi/1.5;

			let cacheNode = document.getElementsByClassName("canvas_main")[0];
			let cacheNum = cacheNode.offsetHeight / this.height;

			// 设置canvas宽高占满div
			this.height = cacheNode.offsetHeight;
			this.width = this.width * cacheNum;

			this.oriWidth = this.width;
			this.oriHeight = this.height;

			this.backgroundCanvas();
			this.initDraw();
		},
		methods: {
			// 背景 + 学生笔迹
			backgroundCanvas () {
				let _this = this;
				const canvas2 = document.getElementById(_this.radom2);
				_this.context2 = canvas2.getContext('2d');
				let bgImgCanvas = new Image();
				bgImgCanvas.setAttribute('crossOrigin', 'Anonymous');
				bgImgCanvas.src = _this.answerUrl;
                _this.context2.clearRect(0, 0, _this.width, _this.height);
				bgImgCanvas.onload = function () {
					_this.context2.drawImage(bgImgCanvas, 0, 0, _this.width, _this.height);
				}
			},
			initDraw (imgUrl) {
				let _this = this;
				// 初始化画布
				const canvas1 = document.getElementById(_this.radom1);
				_this.context1 = canvas1.getContext('2d');
				_this.img.setAttribute('crossOrigin', 'Anonymous');
				if (imgUrl) {
					_this.img.src = imgUrl;
					// 设置canvas宽高
					if(imgUrl) {
						_this.width = _this.oriWidth * _this.scale + '';
						_this.height = _this.oriHeight * _this.scale + '';
					}
				} else {
					_this.img.src = _this.teacherUrl;
				}
				//  加载图片
				_this.context1.clearRect(0, 0, _this.width, _this.height);
				_this.img.onload = function(){
					_this.context1.drawImage(_this.img, 0, 0, _this.width, _this.height);
				}
			},
			// 按下
			canvasDown (e, type) {
				if (this.canDraw) {
					this.canvasMoveUse = true;
					// client是基于整个页面的坐标，offset是cavas距离pictureDetail顶部以及左边的距离
					let canvasX, canvasY;
					if ('touch' === type) {
						canvasX = e.touches[0].clientX - document.getElementById(this.radom1).getBoundingClientRect().left;
						canvasY = e.touches[0].clientY - document.getElementById(this.radom1).getBoundingClientRect().top;
					} else {
						canvasX = e.offsetX;
						canvasY = e.offsetY;
					}
					// 记录起始点和起始状态
					this.beginRec.x = canvasX;
					this.beginRec.y = canvasY;
					this.beginRec.imageData = this.context1.getImageData(0, 0, this.width, this.height);
					// 存储本次绘制坐标信息
					this.drawInfo.push({
						x: canvasX / this.width,
						y: canvasY / this.height,
						type: this.lineType
					});

					if (e.touches && 1 < e.touches.length) {
						this.startTouch = e.touches;
					}
				}
			},
			Area (p0,p1,p2) {
				let area = 0.0 ;
				area = p0.x * p1.y + p1.x * p2.y + p2.x * p0.y - p1.x * p0.y - p2.x * p1.y - p0.x * p2.y;
				return area / 2 ;
			},
			// 计算多边形质心
			getPolygonAreaCenter (points) {
				let sum_x = 0;
				let sum_y = 0;
				let sum_area = 0;
				let p1 = points[1];
				for (var i = 2; i < points.length; i++) {
					let p2 = points[i];
					let area = this.Area(points[0],p1,p2) ;
					sum_area += area ;
					sum_x += (points[0].x + p1.x + p2.x) * area;
					sum_y += (points[0].y + p1.y + p2.y) * area;
					p1 = p2 ;
				}
				return {
					x: sum_x / sum_area / 3,
					y: sum_y / sum_area / 3
				}
			},
			// 根据坐标信息绘制图形
			drawWithInfo () {
				this.info.forEach(item => {
					this.context1.beginPath()
					if (!item.type) {
						// 设置颜色
						this.context1.strokeStyle = item.regionColor;
						this.context1.fillStyle = item.regionColor;
						// 绘制多边形的边
						if (typeof item.region === 'string') {
							item.region = JSON.parse(item.region);
						}
						item.region.forEach(point => {
							this.context1.lineTo(point.x * this.width, point.y * this.height);
						})
						this.context1.closePath();
						// 在多边形质心标注文字
						let point = this.getPolygonAreaCenter(item.region);
						this.context1.fillText(item.areaName, point.x * this.width, point.y * this.height);
					} else if (item.type === 'rec') {
						this.context1.rect(item.x * this.width, item.y * this.height, item.w * this.width, item.h * this.height);
					} else if (item.type === 'circle') {
						this.drawEllipse(this.context1, (item.x + item.a) * this.width, (item.y + item.b) * this.height, item.a > 0 ? item.a * this.width : -item.a * this.width, item.b > 0 ? item.b * this.height : -item.b * this.height);
					}
					this.context1.stroke();
				})
			},
			// 移动时绘制
			canvasMove (e, type) {
				let _this = this;
				let chmapScale = document.getElementById(_this.radom1).style.transform.slice(6,-1) // 获取scale值
				e.preventDefault(); // 取消事件的默认动作。

				if (undefined != e.touches && 1 < e.touches.length && chmapScale < 3.1) {
					let nowTouch = e.touches; // 获取手指点的参数
					let scale = (_this.getDistance(nowTouch[0], nowTouch[1]) / _this.getDistance(_this.startTouch[0], _this.startTouch[1])); // 调用算法算出偏移量
					if (0 === _this.moveTouch) {
						_this.moveTouch = scale;
					}
					if (scale - _this.moveTouch > 0) {
						// 放大
						_this.canvasMoveUse = false;
						_this.magnify(scale - _this.moveTouch); // +0.03
						_this.moveTouch = scale;
					}
					if (scale - _this.moveTouch < 0) {
						// 缩小
						_this.canvasMoveUse = false;
						_this.narrow(scale - _this.moveTouch); // -0.03
						_this.moveTouch = scale;
					}
				} else {
					if (this.canvasMoveUse && this.canDraw && this.beginRec.imageData && this.beginRec.imageData != "") {
						// client是基于整个页面的坐标，offset是cavas距离pictureDetail顶部以及左边的距离
						let canvasX, canvasY;
						if ('touch' === type) {
							canvasX = e.touches[0].clientX - document.getElementById(this.radom1).getBoundingClientRect().left;
							canvasY = e.touches[0].clientY - document.getElementById(this.radom1).getBoundingClientRect().top;
						} else {
							canvasX = e.offsetX;
							canvasY = e.offsetY;
						}
						// 初始化画笔
						this.context1.lineWidth = this.lineWidth;
						if (this.lineType === 'rec') { // 绘制矩形时恢复起始点状态再重新绘制
							this.context1.strokeStyle = this.lineColor;
							this.context1.putImageData(this.beginRec.imageData, 0, 0);
							this.context1.beginPath();
							this.context1.rect(this.beginRec.x, this.beginRec.y, canvasX - this.beginRec.x, canvasY - this.beginRec.y);
							let info = this.drawInfo[this.drawInfo.length - 1];
							info.w = canvasX / this.width - info.x;
							info.h = canvasY / this.height - info.y;
							this.context1.stroke();
						} else if (this.lineType === 'circle') { // 绘制椭圆时恢复起始点状态再重新绘制
							this.context1.strokeStyle = this.lineColor;
							this.context1.putImageData(this.beginRec.imageData, 0, 0);
							this.context1.beginPath();
							let a = (canvasX - this.beginRec.x) / 2;
							let b = (canvasY - this.beginRec.y) / 2;
							this.drawEllipse(this.context1, this.beginRec.x + a, this.beginRec.y + b, a > 0 ? a : -a, b > 0 ? b : -b);
							let info = this.drawInfo[this.drawInfo.length - 1];
							info.a = a / this.width;
							info.b = b / this.height;
							this.context1.stroke();
						}else if (this.lineType === 'line') {// 画线
							this.context1.strokeStyle = this.lineColor;
							this.context1.beginPath();
							this.context1.moveTo(this.beginRec.x,this.beginRec.y);
							this.context1.lineTo(canvasX,canvasY);
							this.context1.stroke();
							this.beginRec.x = canvasX;
							this.beginRec.y = canvasY;
						}else if (this.lineType === 'tick') {// 打勾 相当于画线
							this.context1.strokeStyle = this.lineColor;
							this.context1.beginPath();
							this.context1.moveTo(this.beginRec.x,this.beginRec.y);
							this.context1.lineTo(this.beginRec.x - 10, this.beginRec.y -10);
							this.context1.moveTo(this.beginRec.x,this.beginRec.y);
							this.context1.lineTo(this.beginRec.x + 20, this.beginRec.y - 20);
							this.context1.stroke();
							this.beginRec =  {
								x: '',
								y: '',
								imageData: ''
							};
						}else if (this.lineType === 'halfTick') {// 半勾 相当于画线
							this.context1.strokeStyle = this.lineColor;
							this.context1.beginPath();
							this.context1.moveTo(this.beginRec.x,this.beginRec.y);
							this.context1.lineTo(this.beginRec.x - 10, this.beginRec.y -10);
							this.context1.moveTo(this.beginRec.x,this.beginRec.y);
							this.context1.lineTo(this.beginRec.x + 20, this.beginRec.y - 20);
							this.context1.stroke();
							// 半勾，打叉的坐标
							let x = this.beginRec.x + 10;
							let y = this.beginRec.y - 10;
							this.context1.moveTo(x - 5, y-5);
							this.context1.lineTo(x + 5, y + 5);
							this.context1.stroke();

							this.beginRec =  {
								x: '',
								y: '',
								imageData: ''
							};
						}else if (this.lineType === 'fork') {// 打差 相当于画线
							this.context1.strokeStyle = this.lineColor;
							this.context1.beginPath();
							this.context1.moveTo(this.beginRec.x + 10,this.beginRec.y + 10);
							this.context1.lineTo(this.beginRec.x - 10, this.beginRec.y -10);
							this.context1.stroke();
							this.context1.moveTo(this.beginRec.x + 10,this.beginRec.y - 10);
							this.context1.lineTo(this.beginRec.x - 10, this.beginRec.y + 10);
							this.context1.stroke();
							this.beginRec =  {
								x: '',
								y: '',
								imageData: ''
							};
						}else if (this.lineType === 'rubber') {// 橡皮檫
							this.context1.beginPath();
							this.context1.arc(canvasX, canvasY, 10, 0, 2 * Math.PI);
							this.context1.closePath();
							this.context1.fillStyle = 'white';
							this.context1.fill();
						}else if(this.lineType === 'font') {
							this.context1.fillStyle = this.lineColor;
							this.context1.font = "26px Arial bolder";
							this.context1.fillText(this.fontText, canvasX, canvasY);
							this.beginRec =  {
								x: '',
								y: '',
								imageData: ''
							};
						}else if(this.lineType === 'inputText') {
							this.context1.fillStyle = this.lineColor;
							this.context1.font = "26px Arial bolder";
							this.context1.fillText(this.fontText, canvasX, canvasY);
							this.beginRec =  {
								x: '',
								y: '',
								imageData: ''
							};
						}
					}
                }
			},
			// 绘制椭圆
			drawEllipse (context, x, y, a, b) {
				context.save();
				var r = (a > b) ? a : b;
				var ratioX = a / r;
				var ratioY = b / r;
				context.scale(ratioX, ratioY);
				context.beginPath();
				context.arc(x / ratioX, y / ratioY, r, 0, 2 * Math.PI, false);
				context.closePath();
				context.restore();
			},
			// 抬起
			canvasUp (e, type) {
				let _this = this;
				_this.moveTouch = 0;
				if (_this.canDraw) {
					_this.canvasMoveUse = false;
				}
				let canvas = document.getElementById(_this.radom1);
				_this.canvasUrl1 = canvas.toDataURL('image/png');
			},
			//缩放 勾股定理方法-求两点之间的距离
			getDistance(p1, p2) {
				let x = p2.pageX - p1.pageX
				let y = p2.pageY - p1.pageY;
				return Math.sqrt((x * x) + (y * y));
			},
			/*坐标转换*/
			windowToCanvas(x,y) {
				var box = this.myCanvas.getBoundingClientRect();  //这个方法返回一个矩形对象，包含四个属性：left、top、right和bottom。分别表示元素各边与页面上边和左边的距离
				return {
					x: x - box.left - (box.width - this.myCanvas.width) / 2,
					y: y - box.top - (box.height - this.myCanvas.height) / 2
				};
			},

			// 获取绘制后老师的文件
			getTeacherImgData() {
				let canvas = document.getElementById(this.radom1);
				let base64String = canvas.toDataURL('image/png');
				return base64String;
			},
			// 获取绘制后学生的文件
			getStudentImgData() {
				let canvas = document.getElementById(this.radom2);
				let base64String = canvas.toDataURL('image/png');
				return base64String;
			},

			// 清空画布
			clean (data, correctImage) {
				// 清除笔迹
				this.context1.clearRect(0, 0, this.width, this.height);
			},
			// 批改文字
			write(e) {
				let text = this.inputText.replace(" ", "");
				if(text && text != '') {
					this.isShowWrite = false;
					this.lineType = '';
					// 把换行符换成html
					let textArr = this.inputText.split('\n');
					this.context1.fillStyle = this.lineColor;
					this.context1.font = "26px Arial bolder";

					for(let index = 0; index < textArr.length; index ++) {
						// 把文本写进去
						if(textArr[index] != "") {
							this.context1.fillText(textArr[index], this.tranLeft + this.scrollLeft, this.tranTop + this.scrollTop + ((index+1) * 30));
						}

					}

					this.inputText = '';
					let canvas = document.getElementById(this.radom1);
					this.canvasUrl1 = canvas.toDataURL('image/png');
				}else{
					this.tranLeft = e.offsetX;
					this.tranTop = e.offsetY;
					// this.tranLeft = e.offsetX - this.scrollLeft;
					// this.tranTop = e.offsetY - this.scrollTop;
					this.isShowWrite = true;
				}

			},
			// 批改文字输入框的高
			changeHeight(e) {
				let index = this.inputText.replace(" ", "").split('\n').length;
				if(index > 1) {
					this.tranHeight = (index + 1) * 30
				}
			},

			// 放大
			magnify(num) {
				if(this.scale < 3.5) {
					if(this.canvasUrl1 == null) {
						let canvas = document.getElementById(this.radom1);
						let canvas2 = document.getElementById(this.radom2);
						this.canvasUrl1 = canvas.toDataURL('image/png');
						this.canvasUrl2 = canvas2.toDataURL('image/png');
					}
					if (num) {
						this.scale = parseFloat((this.scale + num));
						this.lineWidth = this.lineWidth + num;
					} else {
						this.scale = parseFloat((this.scale + 0.3).toFixed(1));
						this.lineWidth = this.lineWidth + 0.5;
					}
					this.initDraw(this.canvasUrl1);
					this.backgroundCanvas();
				}
			},

			// 缩小
			narrow(num) {
				if(this.scale > 1) {
					if(this.canvasUrl1 == null) {
						let canvas1 = document.getElementById(this.radom1);
						let canvas2 = document.getElementById(this.radom2);
						this.canvasUrl1 = canvas1.toDataURL('image/png');
						this.canvasUrl2 = canvas2.toDataURL('image/png');
					}
					if (num) {
						this.scale = parseFloat((this.scale + num));
						this.lineWidth = this.lineWidth + num;
					} else {
						this.scale = parseFloat((this.scale - 0.3).toFixed(1));
						this.lineWidth = this.lineWidth - 0.5;
					}
					this.initDraw(this.canvasUrl1);
					this.backgroundCanvas();
				}
			},

			getScroll(e) {
				this.scrollTop = e.target.scrollTop;
				this.scrollLeft = e.target.scrollLeft;
			},

			// 获取DPI
			conversion_getDPI: function () {
				var arrDPI = new Array
				if (window.screen.deviceXDPI) {
					arrDPI[0] = window.screen.deviceXDPI
					arrDPI[1] = window.screen.deviceYDPI
				} else {
					var tmpNode = document.createElement("DIV")
					tmpNode.style.cssText = "width:1in;height:1in;position:absolute;left:0px;top:0px;z-index:99;visibility:hidden"
					document.body.appendChild(tmpNode)
					arrDPI[0] = parseInt(tmpNode.offsetWidth)
					arrDPI[1] = parseInt(tmpNode.offsetHeight)
					tmpNode.parentNode.removeChild(tmpNode)
				}
				return arrDPI
			},
		}

	}
</script>

<style scoped>
    .canvas{
        cursor: crosshair;
    }
    .canvas_container {
        display: flex;
        justify-content: center;
        align-items: flex-end;
        height: 100%;
    }
    .canvas_main {
        height: 100%;
        max-width: 90%;
        overflow: auto;
        position: relative;
    }
    .teacher_canvas {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 19;
    }
</style>