<script context='module'>
    export async function preload(page, token) {
        let id = page.params.id
        let topic = await api.get(`topics?id=${id}`)
        return {topic}
    }
</script>

<script>
    export let topic

    import { Button, Row, Column, Link, PaginationNav } from 'carbon-components-svelte'
    import Add16 from 'carbon-icons-svelte/lib/Add16'
    import { stores, goto } from '@sapper/app'
    import * as api from 'api'

    const {session} = stores()

    let subtopics = []
    let total = 0
    let page = 1
    let res

    let delete = async function(id){
        api.del(`subtopics?id=${id}`, token)
        if (res.yes) {
            subtopics = subtopics.filter(c => c.id != id)
        }
    }

    $:get_subtopics(page)

    let get_subtopics = async function(){
        res = await api.get(`subtopics/from_topic?id=${topic.id}&page=${page}`)
        total = res.total_items
        subtopics = res.data
    }
</script>

<h2>Subtopics in `{topic.name}`</h2>

{#each subtopics as subtopic}
    <Row>
        <Column lg={3} md={3} sm={2}>
            <Link style='font-size: 1.2em; color: white;' href='subtopic/{subtopic.id}'>{subtopic.name}</Link>
        </Column>
        {#if $session.token}
            <Column>
                <Button size='small' hasIconOnly icon={Add16} on:click={() => {goto(`add_entry/${subtopic.id}`)}}/>
            </Column>
        {/if}
    </Row>
{/each}

<PaginationNav bind:page={page} shown={3} loop total={total}/>