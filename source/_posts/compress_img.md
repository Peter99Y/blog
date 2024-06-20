---
title: 图片压缩详解
---

1. 获取 input file 对象;
2. 把 file 对象转成 base64 位字符串;
3. 创建一个 image 元素，赋值 base64 给 image 的 src;
4. 根据创建的 image 元素、宽度、高度绘制 canvas;
5. 通过 canvas 方法 toDataURL(类型，质量(质量只有类型是 image/jpeg 才有效))，得到 canvas 绘制后的 base64;
6. base64 可渲染进行预览 或者 转成 file 文件进行上传;

```
let btn = document.querySelector("input");

btn.onchange = function (event) {
	const file = event.target.files[0]; // file对象
	if (!/^image/.test(file.type)) return alert("请上传图片类型!");
	const fileName = file.name; // 图片名称

	const readObj = new FileReader();
	readObj.readAsDataURL(file); // 传入file对象，获取base64位字符串

	// 等待onload异步读取文件完成
	readObj.onload = (res) => {
		compressImg(res.target.result, null, null, file, fileName);
		// axios上传...
	};
};

// 压缩图片
function compressImg(
	base64,
	quality = 0.92,
	mimeType = "image/jpeg",
	file,
	fileName
) {
	let canvas = document.createElement("canvas");
	let ctx = canvas.getContext("2d");
	let img = document.createElement("img");
	img.src = base64;

	img.onload = () => {
		canvas.width = img.width;
		canvas.height = img.height;

		ctx.drawImage(img, 0, 0, img.width, img.height);

		let newBase64 = canvas.toDataURL(mimeType, quality / 100); // toDataURL返回base64位字符串;
		document.querySelector(".prev-img").setAttribute("src", base64);
		document.querySelector(".next-img").setAttribute("src", newBase64);

		return {
			file,
			base64,
			newBase64,
			newFile: base64ToFile(newBase64, fileName),
			fileName,
		};
	};
}

// base64转file对象
function base64ToFile(base64, fileName) {
	// 将base64 的图片转换成file对象上传 atob将ascii码解析成binary数据
	let binary = atob(base64.split(",")[1]);
	let mimeType = base64.split(",")[0].match(/:(.*?);/)[1];

	let array = [];
	for (let i = 0; i < binary.length; i++) {
		array.push(binary.charCodeAt(i));
	}
	let fileData = new Blob([new Uint8Array(array)], {
		type: mimeType,
	});
	let file = new File([fileData], `${fileName}.${mimeType}`, {
		type: mimeType,
	});
	return file;
}
```
