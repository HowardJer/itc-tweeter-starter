<template>
    <div id="feed">
        <h1>Feed</h1>
        <hr>
        <tweet-create @tweetCreated="loadFollows"></tweet-create>
        <hr>
        <tweet v-for="tweet in tweetsNewestFirst" 
            :key="tweet.tweetId" 
            :tweet="tweet"
            @tweetDeleted="loadFollows"
            @tweetUpdated="loadFollows">
        </tweet>
        <p v-if="!tweets.length">Nothing to show!</p>
    </div>
</template>

<script>
import Tweet from '@/components/Tweet';
import TweetCreate from '@/components/TweetCreate';

export default {
    name: 'Feed',
    components: { 
        Tweet,
        TweetCreate
    },
    data() {
        return {
            limit: 15,
            tweets: []
        }
    },
    computed: {
        tweetsNewestFirst() {
            // sort function from: https://stackoverflow.com/a/8837511
            return this.tweets.slice(0).sort(function(a, b) {
                var keyA = new Date(a.createdAt)
                var keyB = new Date(b.createdAt)
                // Compare the 2 dates
                if (keyA < keyB) return 1;
                if (keyA > keyB) return -1;
                return 0;
            });
        }
    },
    mounted() {
        this.loadFollows();
    },
    methods: {
        loadFollows() {
            this.$axios.request({
                method: 'GET',
                url: 'https://tweeterest.ml/api/follows',
                params: {
                    userId: this.loggedInUser().userId
                }
            })
            .then((response) => {
                let follows = response.data;
                this.loadTweets(this.loggedInUser().userId);
                for (let i in follows) {
                    this.loadTweets(follows[i].userId, true)
                }
            })
        },
        loadTweets(userId, append) {
            this.$axios.request({
                method: 'GET',
                url: 'https://tweeterest.ml/api/tweets',
                params: {
                    userId: userId
                }
            })
            .then((response) => {
                if (response.status === 200) {
                    if (append) {
                        this.tweets = this.tweets.concat(response.data)
                    } else {
                        this.tweets = response.data;
                    }
                }
            })
        }
    }
}
</script>

<style lang="scss" scoped>

</style>