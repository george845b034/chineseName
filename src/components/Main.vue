<template>
	<div class="main">
		<div class="container">
			<div class="row">
				<div class="col-md-9">
					<div class="header clearfix">
						<nav>
							<ul class="nav nav-pills pull-right">
								<li role="presentation" :class="{active: tabActive == 0}" @click="tabActive = 0"><a href="#">姓名</a></li>
								<li role="presentation" :class="{active: tabActive == 1}" @click="tabActive = 1"><a href="#">81吉凶</a></li>
								<li role="presentation" :class="{active: tabActive == 2}" @click="tabActive = 2"><a href="#">Comments</a></li>
							</ul>
						</nav>
						<h3 class="text-muted">Chinese name</h3>
					</div>
					<form class="form-horizontal" v-if="tabActive == 0">
						<div class="form-group">
							<label class="col-sm-2 control-label">筆劃:</label>
							<div class="col-sm-4">
								<select class="form-control" v-model="stroke">
									<option v-for="val in nameData.length">{{val}}</option>
								</select>
							</div>
							<label class="col-sm-2 control-label">五行:</label>
							<div class="col-sm-4">
								<select class="form-control" v-model="fiveElements">
									<option value="gold">金</option>
									<option value="wood">木</option>
									<option value="water">水</option>
									<option value="fire">火</option>
									<option value="soil">土</option>
								</select>
							</div>
						</div>
					</form>
					
					<div class="well" v-if="tabActive == 0">
						<div class="row" v-if="processNameData.length > 0">
							<div class="col-sm-1 text-center padding15 pointer" v-for="val in processNameData" @click="wordSearch(val)">{{val}}</div>
						</div>
						<div class="row" v-else>
							<div class="col-sm-2 text-center">找不到資料</div>
						</div>
					</div>
					<div v-if="tabActive == 1">
						<table class="table table-striped" v-if="processData81.length > 0"> 
							<thead> 
								<tr> 
									<th style="width:45px;">筆劃</th> 
									<th>說明</th> 
									<th style="width:58px;">吉凶</th>
								</tr> 
							</thead> 
							<tbody> 
								<tr v-for="(val, index) in processData81"> 
									<th scope="row">{{index + 1}}</th> 
									<td>{{val['description']}}</td> 
									<td>{{val['result']}}</td>
								</tr> 
							</tbody>
						</table>
						<div class="row" v-else>
							<div class="col-sm-2 text-center">找不到資料</div>
						</div>
					</div>
					<div v-if="tabActive == 2">
						<vue-disqus shortname="chinesename"></vue-disqus>
					</div>
					
					
					<el-dialog v-model="dialogVisible" size="small" :show-close="false">
						<span slot="title" class="dialog-title">
							{{wordTitle}}
						</span>
						<div class="row">
							<div :class="processColumn">
								<h4><span class="label label-warning custom-label">基本</span></h4>
								<strong>部首:</strong> {{wordRadical}}<br/>
								<strong>注音:</strong> {{wordBopomofo}}<br/>
								<strong>筆劃:</strong> {{wordStrokeCount}}
							</div>
							<div :class="processColumn" v-if="wordDefinitions1.length > 0">
								<h4><span class="label label-primary custom-label">動詞</span></h4>
								<div v-for="val in wordDefinitions1">{{val['def']}}</div><br/>
							</div>
							<div :class="processColumn" v-if="wordDefinitions2.length > 0">
								<h4><span class="label label-success custom-label">名詞</span></h4>
								<div v-for="val in wordDefinitions2">{{val['def']}}</div><br/>
							</div>
							<div :class="processColumn" v-if="wordDefinitions3.length > 0">
								<h4><span class="label label-danger custom-label">副詞</span></h4>
								<div v-for="val in wordDefinitions3">{{val['def']}}</div><br/>
							</div>
						</div>
						
						<span slot="footer" class="dialog-footer">
							<el-button @click="dialogVisible = false">取 消</el-button>
						</span>
					</el-dialog>
					
					<div class="text-center">
						<iframe id="preview" src="//partner.huee11.com/tools/books/newnottable.html?apid=george845b034&amp;pcat=books_nbtopm&amp;scat=12&amp;theme=1" border="0" width="530" height="240" frameborder="0" scrolling="no"></iframe>
					</div>
				</div>
				<div class="col-md-3">
					<nav class="bs-docs-sidebar hidden-print hidden-sm hidden-xs affix">
						<!-- [George]廣告1 -->
						<ins class="adsbygoogle"
							style="display:inline-block;width:160px;height:600px"
							data-ad-client="ca-pub-5891811504833462"
							data-ad-slot="8430839125"></ins>
					</nav>
				</div>
			</div>
			
			<footer class="footer">
				<p>© 2017 Company, Inc.</p>
			</footer>
	
		</div>
	</div>
</template>

<script>
	import axios from 'axios'
	import VueDisqus from 'vue-disqus/VueDisqus.vue'

	export default {
		name: 'main',
		components: {
			VueDisqus	
		},
		data() {
			return {
				dialogVisible: false,
				stroke: 1,
				tabActive: 0,
				fiveElements: 'gold',
				nameData: [],
				data81: [],
				wordTitle: '',
				wordStrokeCount: '',
				wordRadical: '',
				wordBopomofo: '',
				wordDefinitions1: [],
				wordDefinitions2: [],
				wordDefinitions3: []

			}
		},
		mounted() {
			let self = this
			let loadingInstance = this.$loading.service({ fullscreen: true, lock: true })
			axios.get('static/dataName.json').then(function (response) {
				self.nameData = response.data
				loadingInstance.close()
			})
			.catch(function (error) {
				console.log(error)
				loadingInstance.close()
			})

			axios.get('static/data81.json').then(function (response) {
				self.data81 = response.data
			})
			.catch(function (error) {
				console.log(error);
			})

			
		},
		computed: {
			processNameData() {
				return (this.nameData.length > 0) ? this.nameData[this.stroke -1][this.fiveElements] : [] ;
			},
			processData81() {
				return (this.data81.length > 0) ? this.data81 : [] ;
			},
			processColumn() {
				var returnString = '';
				var definition1 = this.wordDefinitions1.length > 0 ? 1 : 0
				var definition2 = this.wordDefinitions2.length > 0 ? 1 : 0
				var definition3 = this.wordDefinitions3.length > 0 ? 1 : 0

				switch (definition1 + definition2 + definition3) {
					case 3:
						returnString = 'col-sm-3'
					break
					case 2:
						returnString = 'col-sm-4'
					break
					case 1:
						returnString = 'col-sm-6'
					break
					default:
						returnString = 'col-sm-12'
					break
				}
				return returnString
			}
		},
		methods: {
			wordSearch( inWord ) {
				ga('wordSearch', 'click', inWord);
				let self = this
				axios.get('https://www.moedict.tw/uni/' + inWord).then(function (response) {
					self.dialogVisible = true
					self.wordTitle = response.data.title
					self.wordStrokeCount = response.data.stroke_count
					self.wordRadical = response.data.radical
					if (response.data.heteronyms.length > 0 ) {
						self.wordBopomofo = response.data.heteronyms[0].bopomofo
						self.wordDefinitions1 = []
						self.wordDefinitions2 = []
						self.wordDefinitions3 = []
						for(var key in response.data.heteronyms[0].definitions) {
							if (response.data.heteronyms[0].definitions[key].type == "動") {
								self.wordDefinitions1.push(response.data.heteronyms[0].definitions[key])
							}else if (response.data.heteronyms[0].definitions[key].type == "名") {
								self.wordDefinitions2.push(response.data.heteronyms[0].definitions[key])
							}else{
								self.wordDefinitions3.push(response.data.heteronyms[0].definitions[key])
							}
						}
					}
				})
				.catch(function (error) {
					console.log(error);
				})
			}
		}
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.padding15 {
		padding: 15px;
	}
	ul {
		list-style-type: decimal;
	}
	.pointer {
		cursor: pointer;
		font-size: 24px;
	}
	.dialog-title {
		font-size: 24px;
		font-weight:bold;
	}
	.el-dialog__body {
		padding: 6px 20px;
	}
	.custom-label {
		display: block;
	}
</style>
