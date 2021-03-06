<template>
	<div class="container article">
		<div class="row">
			<div style="width: 100%; position: relative;">
				<div class="post-title">
					<h1>{{Title}}</h1>
				</div>
				<div class="post-meta">
					<span><i>by <a :href="AuthorBlogLink">{{Author}}</a> on {{CreationDateTime}}</i></span>
					<a :href="SteemitArticleLink" target="_blank" style="float: right;"><img class="media-button" src="img/steemit.png"></a>
					<a :href="BusyArticleLink" target="_blank" style="float: right;"><img class="media-button" src="img/busy.png"></a>
				</div>
				<div v-highlightjs v-html="BodyHTML"></div>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
	import Vue from "vue";
	import VueRouter from 'vue-router';
	declare var hljs: any;

	Vue.use(VueRouter);
	import {createPostHtml, formatDate} from "../utils/utils";

	var router = new VueRouter({
		mode: 'history',
		routes: []
	});

	export default Vue.extend({
		props: [
			'author',
			'permlink',
		],
		router,
		data: function ()
		{
			return {
				ArticleURL: '',
				Author: '',
				CreationDateTime: '',
				BodyHTML: '<p>Loading...<p>',
				Title: '',
			}
		},
		directives:
		{
			highlightjs:
			{
				componentUpdated: function (el, binding)
				{
					let targets = el.querySelectorAll('code');
					let i;
					for (i = 0; i < targets.length; ++i)
					{
						hljs.highlightBlock(targets[i]);
					}
				}
			}
		},
		computed:
		{
			AuthorBlogLink() :string
			{
				return 'https://www.steemit.com/@' + this.Author;
			},
			SteemitArticleLink() :string
			{
				return 'https://steemit.com' + this.ArticleURL;
			},
			BusyArticleLink() :string
			{
				return 'https://busy.org' + this.ArticleURL;
			}
		},
		created: function ()
		{
			this.LoadContent();
		},
		methods:
		{
			LoadContent()
			{
				createPostHtml(this.author, this.permlink, (error, blogEntry) =>
				{
					this.ArticleURL = blogEntry.url;
					this.Author = blogEntry.author;
					this.BodyHTML = blogEntry.body;
					this.Title = blogEntry.title;
					
					var options = {year: "numeric", month: "long", day: "numeric", hour: '2-digit', minute:'2-digit', hour12: false};
					this.CreationDateTime = formatDate(blogEntry.created);

				});
			}
		}
	});
</script>

<style scoped>
	div.videoWrapper
	{
		width: 100%;
		height: 0;
		padding-bottom: 56.25%; /* 16/9 = 0.5625 */
		padding-top: 25px;
		position: relative;
	}

	div.videoWrapper iframe
	{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}

	.media-button
	{
		opacity: 1;
		width: 20px;
		height: 20px;
		border-radius: 2px;
		margin-left: 5px;
		float: right;
	}
	
	.media-button:hover
	{
		opacity: 0.75;
	}
	.post-title
	{
		padding:20px 10px;
	}

	.post-meta
	{
		border-top: 1px solid #bdbdbd;
		border-bottom: 1px solid #bdbdbd;
		display: block;
		font-size: 13px;
		font-weight: 400;
		line-height: 21px;
		padding: 10px;
		margin-bottom: 10px;
	}
</style>