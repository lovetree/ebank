<template>
	<div class="purse_user_type">
		<div class="mdui-typo">
			<blockquote class="blockquote_normal">
				<a class="mdui-btn mdui-ripple mdui-color-theme" @click="add(0)"><i class="mdui-icon mdui-icon-left material-icons">add</i>添加</a>
			</blockquote>
			<div class="mdui-divider"></div>
			<blockquote class="blockquote_normal">
				名称：<input class="mdui-textfield-input input_normal" type="text" v-model="keyword.name" />
				Alias：<input class="mdui-textfield-input input_normal" type="text" v-model="keyword.alias" />
				<a class="mdui-btn mdui-ripple mdui-color-theme" @click="search(1)"><i class="mdui-icon mdui-icon-left material-icons">search</i>搜索</a>
			</blockquote>
		</div>
		<div class="mdui-table-fluid">
			<table class="mdui-table mdui-table-hoverable">
				<thead>
				<tr>
					<th>#</th>
					<th>ID</th>
					<th>类型名称</th>
					<th>Alias</th>
					<th>状态</th>
					<th>备注</th>
					<th>创建时间</th>
					<th>修改时间</th>
					<th>操作</th>
				</tr>
				</thead>
				<tbody>
				
				<tr v-for="(val,key,index) in list.data">
					<td v-text="key+1"></td>
					<td v-text="val.id"></td>
					<td v-text="val.name"></td>
					<td v-text="val.alias"></td>
					<td v-text="val.status ? '启用' : '禁用'"></td>
					<td v-text="val.remarks"></td>
					<td v-text="val.created_at"></td>
					<td v-text="val.updated_at"></td>
					<td>
						<div class="mdui-btn-group" v-if="val.id > 3">
							<a class="mdui-btn mdui-ripple mdui-color-theme" @click="add(val.id)">修改</a>
							<a class="mdui-btn mdui-ripple mdui-color-deep-orange" @click="del(val.id)">删除</a>
						</div>
					</td>
				</tr>
				</tbody>
			</table>
		</div>
		
		<!--修改弹窗-->
		<div class="mdui-dialog dialog_add">
			<div class="mdui-dialog-title">
				身份类型新增/修改
			</div>
			<div class="mdui-dialog-content">
				<form>
					<div class="mdui-container">
						<div class="mdui-textfield">
							<label class="mdui-textfield-label">类型名称</label>
							<input class="mdui-textfield-input" type="text" v-model="form.name" />
						</div>
					</div>
					<div class="mdui-container">
						<div class="mdui-textfield">
							<label class="mdui-textfield-label">Alias 英文别名</label>
							<input class="mdui-textfield-input" type="text" v-model="form.alias" />
						</div>
					</div>
					<div class="mdui-container">
						转账使用字符串拼接
					</div>
					<div class="mdui-container">
						<label class="mdui-radio">
							<input type="radio" name="status" v-model="form.status" value="1" :checked="!!form.status" />
							<i class="mdui-radio-icon"></i>
							启用
						</label>
					</div>
					<div class="mdui-container">
						<label class="mdui-radio">
							<input type="radio" name="status" v-model="form.status" value="0" :checked="!form.status" />
							<i class="mdui-radio-icon"></i>
							禁用
						</label>
					</div>
					<div class="mdui-container">
						<div class="mdui-textfield">
							<label class="mdui-textfield-label">备注</label>
							<input class="mdui-textfield-input" type="text" v-model="form.remarks" />
						</div>
					</div>
				</form>
			</div>
			<div class="mdui-dialog-actions">
				<a class="mdui-btn mdui-ripple" mdui-dialog-close>取消</a>
				<a class="mdui-btn mdui-ripple mdui-color-theme" @click="add_submit">提交</a>
			</div>
		</div>
		
		<div class="mdui-color-white footer">
			<pagination
					:pageInfo="{
						total:list.total,
						current:list.current_page,
						pagenum:list.per_page,
						page:list.last_page,
						pagegroup:9,
						skin:'#2196F3'
					}"
					@change="search"
			></pagination>
		</div>
	</div>
</template>
<script>
	export default {
		data(){
			return {
				list : [],
				form : '',
				dialog : '',
				keyword : {
					page : 1,
					name : '',
					alias : '',
				},
			};
		},
		methods : {
			add(id){
				let t = this;
				t.dialog.open();
				get('/purse/user_type_detail',{id:id},function(data){
					t.form = data;
				});
			},
			add_submit(){
				let t = this;
				post('/purse/user_type',t.form,function(){
					t.dialog.close();
					t.init();
				});
			},
			del(id){
				let t = this;
				mdui.confirm('删除后数据不可恢复，确认删除请点击【确定】按钮', '确认？', function(){
					del('/purse/user_type',{id:id},function(){
						t.init();
					});
				},function(){},{history:false,confirmText:'确定',cancelText:'取消'});
			},
			search(page){
				this.keyword.page = page;
				this.init();
			},
			init(){
				let t = this;
				get('/purse/user_type',t.keyword,function(data){
					t.list = data;
				});
			}
		},
		mounted(){
			let t = this;
			t.dialog = new mdui.Dialog('.dialog_add',{history:false});
			t.init();
		}
	}
</script>