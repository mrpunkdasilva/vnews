<template>
	<main class="post-page">
		<section v-if="author" class="container mx-auto p-4 bg-win-gray border-2 border-t-win-white border-l-win-white border-b-win-black border-r-win-black">
			<div class="flex items-center mb-4">
				<img
					:src="CreateURL(author.avatar)"
					class="inline-block w-16 h-16 mr-4 border-2 border-t-win-white border-l-win-white border-b-win-black border-r-win-black"  />

				<h1 class="text-win-black text-2xl uppercase font-bold">
					{{ author.full_name }}
				</h1>
			</div>

			<p class="text-win-black mb-8">{{ author.short_bio }}</p>

			<div class="grid gap-4" v-if="author.posts">
				<PostCard
					v-for="(post, i) in author.posts"
					:key="i"
					:post="post" />
			</div>
		</section>
	</main>
</template>

<script>
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'
import sanity from '../../client'
import { CreateURL } from '../../utils'

import PostCard from '../../components/PostCard'

export default {
	components: {
		PostCard
	},
	setup () {
		const route = useRoute()
		const id = ref(route.params.id)
		const author = ref(null)

		onMounted(() => {
			const query = '*[_type == "author" && _id == $id][0] { ..., "posts": *[_type == "post" && author->_id == $id] {_id, title, excerpt, image, _createdAt, author->{full_name, avatar}}}'
			const params = { id: id.value }
			sanity.fetch(query, params).then(data => {
				author.value = data
			})
		})

		return {
			author,
			CreateURL
		}
	}
}
</script>
