<template>
	<div class="editor">
		<div ref="toolbar" class="toolbar">
		</div>
		<div ref="editor" class="text">
		</div>
		<button type="button" class="commit" @click="commit()">提交</button>
	</div>
</template>

<script>
	import E from 'wangeditor';
	export default {
		name: 'editoritem',
		data() {
			return {
				// uploadPath,
				editor: null,
				info_: null
			}
		},
		model: {
			prop: 'value',
			event: 'change'
		},
		props: {
			value: {
				type: String,
				default: ''
			},
			isClear: {
				type: Boolean,
				default: false
			}
		},
		watch: {
			isClear(val) {
				// 触发清除文本域内容
				if (val) {
					this.editor.txt.clear()
					this.info_ = null
				}
			},
			value: function(value) {
				if (value !== this.editor.txt.html()) {
					this.editor.txt.html(this.value)
				}
			}
			//value为编辑框输入的内容，这里我监听了一下值，当父组件调用得时候，如果给value赋值了，子组件将会显示父组件赋给的值
		},
		mounted() {
			this.seteditor()
			this.editor.txt.html(this.value)
		},
		methods: {
			commit(){
				alert(this.editor.txt.html());
			},
			seteditor() {
				// http://192.168.2.125:8080/admin/storage/create
				this.editor = new E(this.$refs.toolbar, this.$refs.editor)
				this.editor.customConfig.uploadImgShowBase64 = false // base 64 存储图片
				this.editor.customConfig.uploadImgServer = 'http://otp.cdinfotech.top/file/upload_images'// 配置服务器端地址
				this.editor.customConfig.uploadImgHeaders = { }// 自定义 header
				this.editor.customConfig.uploadFileName = 'file' // 后端接受上传文件的参数名
				this.editor.customConfig.uploadImgMaxSize = 2 * 1024 * 1024 // 将图片大小限制为 2M
				this.editor.customConfig.uploadImgMaxLength = 6 // 限制一次最多上传 3 张图片
				this.editor.customConfig.uploadImgTimeout = 3 * 60 * 1000 // 设置超时时间

				// 配置菜单
				this.editor.customConfig.menus = [
					'bold', // 粗体
					'italic', // 斜体
					'underline', // 下划线
					'strikeThrough', // 删除线
					'foreColor', // 文字颜色
					'link', // 插入链接
					'emoticon', // 表情
				]

				this.editor.customConfig.uploadImgHooks = {
					fail: (xhr, editor, result) => {
						// 插入图片失败回调
					},
					success: (xhr, editor, result) => {
						// 图片上传成功回调
					},
					timeout: (xhr, editor) => {
						// 网络超时的回调
					},
					error: (xhr, editor) => {
						// 图片上传错误的回调
					},
					customInsert: (insertImg, result, editor) => {
						// 图片上传成功，插入图片的回调
						//result为上传图片成功的时候返回的数据，这里我打印了一下发现后台返回的是data：[{url:"路径的形式"},...]
						// console.log(result.data[0].url)
						//insertImg()为插入图片的函数
						//循环插入图片
						// for (let i = 0; i < 1; i++) {
						// console.log(result)
						let url = "http://otp.cdinfotech.top" + result.url
						insertImg(url)
						// }
					}
				}
				this.editor.customConfig.onchange = (html) => {
					this.info_ = html // 绑定当前逐渐地值
					this.$emit('change', this.info_) // 将内容同步到父组件中
				}
				// 创建富文本编辑器
				this.editor.create()
			}
		}
	}
</script>

<style scoped="scoped">
	.editor {
		width: 60%;
		/* height: 300px; */
		margin: 0 auto;
		position: relative;
		z-index: 0;
		background-color: rgba(255,255,255,0.7);
		font-size: 20px;
	}

	.toolbar {
		border: 1px solid #ccc;
	}
	.w-e-menu{
		/* color: white; */
	}

	.text {
		border: 1px solid #ccc;
		height: 200px;
		min-height: 300px;
		color: #000000;
	}
	.text:hover {
		cursor: auto;
	}
	.commit{
		cursor: pointer;
		margin: 10px 20px;
		float: right;
		width: 100px;
		height: 40px;
		background-color: orange;
		font-size: 20px;
	}
	.commit:hover{
		background-color: orangered;
	}
</style>
