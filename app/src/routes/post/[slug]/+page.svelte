<script lang="ts">
	import { PortableText } from '@portabletext/svelte';
	import { formatDate } from '$lib/utils';
	import { urlFor } from '$lib/utils/image';
	import type { PageData } from './$types';
	import { getUnsplashCredit, type UnsplashCredit } from '$lib/utils/sanity';

	export let data: PageData;

	const unsplashCredit = async ()=>{
		const metadata:UnsplashCredit = await getUnsplashCredit(data.slug.current);
		
		if (!metadata.mainImage.asset) return Promise.resolve('');
		const formatted:string = `<a href=${metadata.mainImage.asset?.source.url}>${metadata.mainImage.asset?.creditLine}</a>`;
		return formatted;
	}

</script>

<section class="post">
	{#if data.projectCategory}
		<h3 class="post__category">▶︎ {data.projectCategory}</h3>
	{/if}
	{#if data.mainImage}
		<img
			class="post__cover"
			src={urlFor(data.mainImage).url()}
			alt="Cover image for {data.title}"
		/>
		{#await unsplashCredit()}
			<hr />
		{:then credit} 
		    <div class="image-caption">{@html credit}</div>
		{/await}
	{/if}
	<div class="post__container">
		<h1 class="post__title">{data.title}</h1>
		<p class="post__excerpt">{data.excerpt}</p>
		<p class="post__date">
			{formatDate(data._createdAt)}
		</p>
		<div class="post__content">
			<PortableText value={data.body} />
		</div>
	</div>
</section>

<style>
	.image-caption {
		font-size: 0.8rem;
		text-align: center;
	}
</style>