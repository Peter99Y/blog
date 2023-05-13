---
title: 上传图片压缩
---

```
(function () {
	var btn = document.querySelector("input");
	var prevImg = document.querySelector(".prev-img");
	
	btn.onchange = function (event) {
		const readObj = new FileReader();
		const file = event.target.files[0];
		const type = file.type;
		const fileName = file.name;
		const originalSize = file.size / 1024;

		if (!/^image/.test(type)) return alert("请上传图片类型!");

		// 作为一个Base64字符串 成为readObj.result的内容;
		readObj.readAsDataURL(file);

		// onload读取文件完成事件
		readObj.onload = () => {
			prevImg.setAttribute("src", readObj.result);
			compressImg(readObj.result, 40, "image/jpeg", fileName);
		};
	};

	function compressImg(
		base64,
		quality,
		mimeType = "image/jpeg",
		fileName = "compressed_Image"
	) {
		let canvas = document.createElement("canvas");
		let img = document.createElement("img");
		img.src = base64;

		img.onload = () => {
			canvas.width = img.width;
			canvas.height = img.height;

			let ctx = canvas.getContext("2d");
			ctx.drawImage(img, 0, 0, img.width, img.height);
			// toDataURL方法返回一个base64位字符串;
			let newImgBase = canvas.toDataURL(mimeType, quality / 100);
			let compressedSize = base64ImageSize(newImgBase);

			document.querySelector(".next-img").setAttribute("src", newImgBase);
			return;
			const a = document.createElement("a");
			a.download = fileName;
			a.setAttribute("accept", mimeType);
			a.href = newImgBase;
			a.click();
			a.remove();
		};
	}

	// 获取base64大小
	function base64ImageSize(base64ImageStr) {
		const indexBase64 = base64ImageStr.indexOf("base64,");
		if (indexBase64 < 0) return -1;
		const str = base64ImageStr.substr(indexBase64 + 6);
		return ((str.length * 0.75) / 1024).toFixed(2);
	}
})();
```