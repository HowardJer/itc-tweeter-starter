<template>
    <button @click="toggleLike">
        {{ liked ? 'Unlike' : 'Like' }} ({{ count }})
    </button>
</template>

<script>
export default {
    name: 'TweetCommentLike',
    props: [ 'commentId' ],
    data() {
        return {
            likes: []
        }
    },
    mounted() {
        this.loadLikes();
    },
    computed: {
        count() {
            return this.likes.length;
        },
        liked() {
            console.log(this.likes)
            let match = this.likes.filter((like) => {
                return like.userId === this.loggedInUser().userId;
            })
            
            return match.length > 0;
        }
    },
    methods: {
        loadLikes() {
            this.$axios.request({
                method: 'GET',
                url: 'https://tweeterest.ml/api/comment-likes',
                params: {
                    commentId: this.commentId
                }
            })
            .then((response) => {
                console.log('LIKES', response.data);
                this.likes = response.data;
            })
        },
        toggleLike() {
            // if already liked
            if (this.liked) {
                this.$axios.request({
                    method: 'DELETE',
                    url: 'https://tweeterest.ml/api/comment-likes',
                    data: {
                        loginToken: this.loginToken(),
                        commentId: this.commentId
                    }
                })
                .then((response) => {
                    console.log(response);
                    this.loadLikes();
                })
            }
            // if not already liked
            else {
                this.$axios.request({
                    method: 'POST',
                    url: 'https://tweeterest.ml/api/comment-likes',
                    data: {
                        loginToken: this.loginToken(),
                        commentId: this.commentId
                    }
                })
                .then((response) => {
                    console.log(response);
                    this.loadLikes();
                })
            }
        }
    }
}
</script>

<style lang="scss" scoped>

</style>