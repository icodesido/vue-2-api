<template>
	<section>
	    <ul>
	        <li v-for="filteredItem of filteredList" :key="filteredItem.id">
	        	<p><strong>User ID</strong> {{ filteredItem.userId }} </p>
	        	<p><strong>Post ID</strong> {{ filteredItem.postId }} </p>
	            <p><strong>Post Title</strong> {{ filteredItem.postTitle }} </p>	            
	            <p><strong>Body</strong> {{ filteredItem.body }} </p>
	            <p><strong>Album Id</strong> {{ filteredItem.id }} </p>
	            <p><strong>Album Title</strong> {{ filteredItem.title }} </p>	            
	            <p><strong>Thumbnail</strong> <img :src= "filteredItem.thumbnailUrl" :alt="filteredItem.title" /> </p>
	        </li>
	    </ul>
   </section>
</template>
<script>
    import axios from 'axios';
    export default {
        data: () => ({
            posts: [],
            albums: [],
            mergedList: [],
        }),

        beforeCreate() {
            const postsPromise = axios.get('https://jsonplaceholder.typicode.com/posts');
            const albumsPromise = axios.get('https://jsonplaceholder.typicode.com/photos?albumId=1');

            Promise
				.all([postsPromise, albumsPromise])
				.then(([postsResponse, albumsResponse]) => {
					this.posts = postsResponse.data;
					this.albums = albumsResponse.data;

					this.renamePostsKeys();
					this.mergeAPIs();
				});
        },

        methods: {
        	renamePostsKeys() {
				this.posts = this.posts.map( (obj) => 
					({
						userId: obj.userId,
						body: obj.body,
						postTitle: obj.title,
						postId: obj.id
					})
				);        		
        	},

            mergeAPIs() {				
				this.posts.forEach( (item, i) => this.mergedList.push(Object.assign({}, item, this.albums[i])) )
             }
        },

        computed: {
        	filteredList: function() {
        		return this.mergedList.filter(post => post.userId === 3);
        	}
        }
    }
</script>
<style scoped>
    ul {
        display: flex;
        flex-wrap: wrap;
    }

    li {
    	display: flex;
    	flex-direction: column;
    	justify-content: space-between;
        width: 18%;
        margin-bottom: 21px;
        margin-left: 20px;
        padding: 10px;
        border: 1px solid;
        box-sizing: border-box;
    }

    img {
    	display: block;
    	margin: 10px auto;
    }
</style>