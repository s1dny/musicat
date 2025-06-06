<script lang="ts">
    import { fly } from "svelte/transition";
    import type { ArtistProject } from "../../App";
    import { db } from "../../data/db";
    import { autoWidth } from "../../utils/AutoWidth";
    import { flip } from "svelte/animate";
    import { quadInOut } from "svelte/easing";
    import Icon from "../ui/Icon.svelte";

    export let artist: ArtistProject;

    $: members = artist?.members ?? [];

    let newMember = "";
    let isEditEnabled = false;

    function addMember() {
        if (members.includes(newMember)) return;

        members.push(newMember);

        db.artistProjects.update(artist.id, {
            members: [...members],
        });
        newMember = "";
    }

    function removeMember(memberName) {
        members.splice(
            members.findIndex((m) => m === memberName),
            1,
        );
        db.artistProjects.update(artist.id, {
            members,
        });
    }
</script>

<container>
    <h2>Members:</h2>
    <div class="content">
        {#if members.length}
            <p class="label">project members:</p>
            <div class="members">
                {#each members as member (member)}
                    <div
                        animate:flip={{ duration: 180, easing: quadInOut }}
                        class="member"
                        class:editable={isEditEnabled}
                    >
                        <p>{member}</p>
                        {#if isEditEnabled}
                            <Icon
                                icon="mingcute:close-circle-fill"
                                onClick={() => removeMember(member)}
                            />
                        {/if}
                    </div>
                {/each}
            </div>
        {/if}
        {#if isEditEnabled}
            <form on:submit|preventDefault={addMember}>
                <input
                    use:autoWidth
                    autofocus
                    class="member-input"
                    bind:value={newMember}
                    placeholder="Add member"
                />
            </form>
        {/if}
        <div
            class="edit-button"
            on:click={() => {
                isEditEnabled = !isEditEnabled;
            }}
        >
            <Icon icon="clarity:edit-solid" />
        </div>
    </div>
</container>

<style lang="scss">
    .content {
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: 10px;
        justify-content: flex-start;
    }
    container {
        display: block;
        padding: 2em;
    }

    p {
        margin: 0;
    }

    input {
        padding: 0;
        font-size: 14px;
        outline: none;
        background: none;
        min-width: 120px;
        border: none;
        &::placeholder {
            color: rgb(105, 105, 105);
        }
    }

    .members {
        display: flex;
        flex-direction: row;
        gap: 10px;
        align-items: center;
    }

    .member {
        position: relative;
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: 5px;
        padding: 0 1px;

        &.editable {
            background: rgb(62, 61, 61);
            border-radius: 4px;
            border: 1px solid rgb(76, 72, 72);
            padding: 0 6px;
        }

        &:hover {
        }
        p {
            margin: 0;
        }

        &:not(:last-child) {
            p::after {
                content: ",";
                position: absolute;
                right: -6px;
                top: 0;
            }
        }
    }

    .edit-button {
        border-radius: 4px;
        width: 20px;
        height: 20px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: grey;
        &:hover {
            background-color: rgba(170, 170, 170, 0.418);
            color: white;
        }
    }

    .label {
        opacity: 0.6;
    }
</style>
