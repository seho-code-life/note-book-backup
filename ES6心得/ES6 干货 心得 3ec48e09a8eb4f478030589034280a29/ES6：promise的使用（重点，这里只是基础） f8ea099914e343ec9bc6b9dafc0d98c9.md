# ES6：promise的使用（重点，这里只是基础）

# ES6：promise的使用（重点，这里只是基础）

// let state=1;

// function step1(resolve,reject){

// console.log("开始洗菜做饭");

// if(state==1){

// resolve("洗菜做饭完成");

// }else{

// reject("洗菜做饭失败");

// }

// }

// function step2(resolve,reject){

// console.log("开始坐下来吃饭");

// if(state==1){

// resolve("开始坐下来吃饭完成");

// }else{

// reject("开始坐下来吃饭失败");

// }

// }

// function step3(resolve,reject){

// console.log("开始收拾桌子洗完'");

// if(state==1){

// resolve("开始收拾桌子洗完完成");

// }else{

// reject("开始收拾桌子洗完失败");

// }

// }

// new Promise(step1).then(function(val){

// console.log(val);

// return new Promise(step2);

// }).then(function(){

// console.log(val);

// return new Promise(step3);

// }).then(function(val){

// console.log(val);

// return val;

// })