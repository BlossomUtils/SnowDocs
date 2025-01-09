<script>
    import { Carta } from 'carta-md';

    /** @type {import('./$types').PageData} */
    export let data;
    const carta = new Carta({
        sanitize: false
    });

    let html = '';
    let error = null;

    async function renderMarkdown() {
        try {
            if (data.props.markdown) {
                html = await carta.render(data.props.markdown);
            } else {
                throw new Error('No file found at this location');
            }
        } catch (err) {
            error = err;
        }
    }

    $: if (data.props.markdown) {
        renderMarkdown();
    }

    $: console.log('Current slug:', data.props.slug);
</script>

<div class="flex">
    <!-- Sidebar -->
    <div class="w-64 h-screen bg-surface-700/5 p-4 border-r border-surface-500/20">
        <nav class="space-y-1">
            {#each data.props.files as file}
                {@const isActive = data.props.slug === file.path}
                <a
                    href="/docs/{file.path}"
                    class="block px-4 py-2 rounded-lg {isActive ? 'bg-primary-500 text-white' : 'hover:bg-surface-500/10'}"
                >
                    {file.title}
                </a>
            {/each}
        </nav>
    </div>

    <div class="flex-1 container mx-auto px-4 py-8">
        {#if error}
            <p class="text-error-500">Error: {error.message}</p>
        {:else if !html}
            <p class="text-surface-500">Loading...</p>
        {:else}
            <div class="prose prose-slate dark:prose-invert max-w-none">
                {@html html}
            </div>
        {/if}
    </div>
</div>