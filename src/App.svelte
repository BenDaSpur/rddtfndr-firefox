<script>
    import { onMount } from "svelte";
    import Card from "./components/Card.svelte";
    import Heading from "./components/Heading.svelte";
    import Loading from "./components/Loading.svelte";
    import Navbar from "./components/Navbar.svelte";
    import { coins, currency, limit, search, filtered } from "./stores/coins";
    import { loading } from "./stores/loading.js";
    import { Container, Row, Col } from "sveltestrap";

    let thePosts;
    let postsLoading = false;
    async function getPosts() {
        postsLoading = true;
        browser.tabs
            .query({ currentWindow: true, active: true })
            .then(async (tabs) => {
                let tab = tabs[0]; // Safe to assume there will only be one result
                console.log(tab.url);
                let res = await fetch(
                    `https://reddit.com/submit.json?url=${tab.url}`
                );
                if (res.ok) {
                    postsLoading = false;
                    thePosts = await res.json();
                    console.log(thePosts);
                }
            }, console.error);
    }

    onMount(() => {
        getPosts();
    });
</script>

<svelte:head>
    <!-- <link
        href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css"
        rel="stylesheet"
    /> -->
</svelte:head>

<main>
    <Container>
        {#if postsLoading}
            <Loading />
        {/if}

        {#if thePosts}
            {#each thePosts as post}
                {#if post.data.children[0].data.title}
                    <Row>
                        <Col xs={3}>
                            <a
                                href={`https://reddit.com/r/${post.data.children[0].data.subreddit}`}
                            >
                                /r/{post.data.children[0].data.subreddit}
                            </a>
                        </Col>
                        <Col xs={9}>
                            <Row>
                                <Col>
                                    <a
                                        href={`https://reddit.com${post.data.children[0].data.permalink}`}
                                    >
                                        {post.data.children[0].data.title}
                                    </a>
                                </Col>
                            </Row>
                            <Row>
                                <Col>
                                    {post.data.children[0].data.num_comments} comments
                                </Col>
                            </Row>
                        </Col>
                    </Row>
                {/if}
            {/each}
        {:else}
            No posts found
        {/if}

        <!-- {#each $filtered as item}
                <Card
                img_url={item.image}
                rank={item.market_cap_rank}
                name={item.name}
                price={item.current_price}
                market_cap={item.market_cap}
                h24={item.price_change_percentage_24h}
                />
                {/each} -->
    </Container>
</main>
