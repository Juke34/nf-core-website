<script>
    import Markdown from "svelte-exmarkdown";
    import emoji from "remark-emoji";
    import remarkGfm from "remark-gfm";
    import remarkDirective from "remark-directive";
    import admonitionsPlugin from "@root/bin/remark-admonitions.js";
    import addClasses from "rehype-add-classes";
    import rehypeAutolinkHeadings from "rehype-autolink-headings";
    // import rehypePrettyCode from 'rehype-pretty-code';
    import rehypeSlug from "rehype-slug";
    import urls from "rehype-urls";
    import rehypeWrap from "rehype-wrap-all";
    import { h } from "hastscript";
    import remarkMath from "remark-math";
    import rehypeKatex from "rehype-katex";

    export let md;

    if (md) {
        // replace newline with <br>
        md = md.replace(/(\n)/g, "  \n");
    }
</script>

<Markdown
    {md}
    plugins={[
        {
            remarkPlugin: [emoji, remarkGfm, remarkDirective, admonitionsPlugin, remarkMath],
            rehypePlugin: [
                rehypeSlug,
                [
                    rehypeAutolinkHeadings,
                    {
                        behavior: "append",
                        content: h("i.ms-1.fas.fa-link.fa-xs.invisible"),
                    },
                ],
                [
                    addClasses,
                    {
                        table: "table table-hover table-sm small",
                    },
                ],
                [
                    rehypeWrap,
                    {
                        selector: "table",
                        wrapper: "div.table-responsive",
                    },
                ],
                [
                    urls,
                    (url) => {
                        const regex = /^https:\/\/(raw.)*github/;
                        if (!regex.test(url.href) && url.href?.endsWith(".md")) {
                            url.href = url.href.replace(/\.md$/, "/");
                            url.pathname = url.pathname.replace(/\.md$/, "/");
                            url.path = url.path.replace(/\.md$/, "/");
                        } else if (!regex.test(url.href) && url.href?.endsWith(".mdx")) {
                            url.href = url.href.replace(/\.mdx$/, "/");
                            url.pathname = url.pathname.replace(/\.mdx$/, "/");
                            url.path = url.path.replace(/\.mdx$/, "/");
                        }
                    },
                ],
                rehypeKatex,
                // [ // vite doesn't like to compile rehype-pretty-code
                //     rehypePrettyCode,
                //     {
                //         langPrefix: 'language-',
                //     },
                // ],
            ],
        },
    ]}
/>
