<template>
	<div>
		<el-col>
			<el-col :md="5" :xs="24" id="CommentPercent">
				<span>好评度：</span>
				<div>{{rate}}%</div>
			</el-col>
			<el-col :md="19" :xs="24" id="tag">
				<el-button v-for="i in 6" :key="i" style="padding: 0">
					<el-tag type="info">遍历出6个tag</el-tag>
				</el-button>
			</el-col>
		</el-col>
		<el-col style="border-bottom: 1px solid #ddd;padding: 15px" v-for="i in comments" :key="i.id">
			<el-col :md="5" :xs="12">
				<div class="UserComments" style="display: flex">
					<img src="https://misc.360buyimg.com/user/myjd-2015/css/i/peisong.jpg"
						width="25px" height="25px" alt="UsrName" style="border-radius: 50%;margin-right: 5px;">
					<span style="margin: auto" v-text="i.user_name"></span>
				</div>
				<div style="display: flex;">
					<div style="width: 25px"></div>
					<span style="margin: auto"></span>
				</div>
			</el-col>
			<el-col :md="19" :xs="24" id="comments">
				<el-rate
						v-model="i.rank"
						disabled
						show-score
						text-color="#ff9900"
						score-template="{value} points">
				</el-rate>
				<el-col>
					<span v-text="i.content"></span>
				</el-col>
				<el-col>
					<span style="margin: 0">评论时间：{{i.create_time}}</span>
				</el-col>
			</el-col>
		</el-col>
		<!--comment-->
		<el-col style="margin: 5% 0">
			<el-col :md="5" :xs="12">
				<div class="UserComments" style="display: flex">
					<img src="https://misc.360buyimg.com/user/myjd-2015/css/i/peisong.jpg"
						width="25px" height="25px" alt="UsrName" style="border-radius: 50%;margin-right: 5px;">
					<span style="margin: auto">{{$global.user.username}}</span>
				</div>
				<div style="display: flex;">
					<div style="width: 25px"></div>
					<span style="margin: auto">vip1</span>
				</div>
			</el-col>
			<el-col :span="19"  :xs="24" style="text-align: left">
				<el-col style="margin: 0 0 10px 0">
					<el-col :md="4" :xs="6">
						<span>选择评分</span>
					</el-col>
					<el-col :md="14" :xs="18">
						<el-rate v-model="commentRate" :colors="['#99A9BF', '#F7BA2A', '#FF9900']"></el-rate>
					</el-col>
				</el-col>
				<el-col>
					<el-col :md="18"  :xs="24">
						<textarea rows="3" v-model="comment" placeholder="在此添加您的评论" id="myComment"></textarea>
					</el-col>
					<el-col :md="4" style="float: right" class="hidden-xs-only">
						<el-button style="height: 100%" type="primary" @click="raiseComment">发表评论</el-button>
					</el-col>
					<el-col :span="10" class="hidden-sm-and-up" style="margin: 20px 0 70px 0">
						<el-button style="height: 100%" type="primary" @click="raiseComment">发表评论</el-button>
					</el-col>
				</el-col>
			</el-col>
		</el-col>
	</div>

</template>

<script>
import axios from 'axios'
export default {
	name: "ProductComments",
	data(){
		return {
			rate: parseInt(Math.random()*100),
			date: new Date(),
			comment: null,
			commentRate:null,
			comments:[]
		}
	},
	methods:{
		getcounts(){
			this.rate = parseInt(Math.random()*100)
		},
		raiseComment(){
            if (this.comment === null || this.comment === ""){
                this.$message.warning("请输入评论内容")
            }
            else if (this.commentRate === null || this.commentRate === 0){
                this.$message.warning("请您评分")
            }
            else{
                this.$message.warning("正在上传")
                axios.post('/api/v0/agency/comments',{
					content:this.comment,
					rank:this.commentRate,
					user_id:this.$global.user.id,
					agency_id:1
				})
						.then(response=>{
						})
						.catch(error=>{
							this.$message.error(error.message)
						})
				this.comments.push({
					content:this.comment,
					rank:this.commentRate,
					user_name:this.$global.user.username,
					agency_id:this.$global.shopList.id,
					create_time:this.date
				})
            }
		},
		getComment(){
			axios.get('/api/v0/agency/comments',{
				params: {
					agency_id: this.$global.shopList.id
				}
			})
					.then(responese=>{
				this.comments = responese.data.comments
			})
		}
	},
	created:function () {
		this.getComment()
	}
}
</script>

<style scoped>
.title{
	margin-top: 2%;
}
#CommentPercent {
	padding: 15px 0 0 40px;
	float: left;
	text-align: left;
}
#CommentPercent span{
	font-size: 12px;
	color: #666;
	font-weight: 400;
}
#CommentPercent div{
	line-height: 110%;
	font-size: 45px;
	color: #f7ba2a;
	font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
}
#tag{
	text-align: left;
}
#tag button{
	margin: 1% 1%;
}
#tag span{

}
.UserComments span{
	color: #666;
}
#comments{
	text-align: left;
}
#comments span{
	margin: 2% 0;
	display: block;
}
#myComment:focus{
	background-color:#f0f3ef;
}
#myComment{
	display: block;
	resize: vertical;
	padding: 5px 15px;
	line-height: 1.5;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
	width: 100%;
	font-size: inherit;
	color: #606266;
	background-color: #fff;
	background-image: none;
	border: 1px solid #dcdfe6;
	border-radius: 4px;
	-webkit-transition: border-color .2s cubic-bezier(.645,.045,.355,1);
	transition: border-color .2s cubic-bezier(.645,.045,.355,1);
}
</style>