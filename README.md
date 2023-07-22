# Astro minimal project with view transitions

See https://docs.astro.build/en/guides/view-transitions/.

Total generated JS (it is in `<script type="module">` tag on every page):

```js
let o = history.state?.index || 0;
history.state || history.replaceState({
    index: o
}, document.title);
const m = !!document.startViewTransition,
    s = () => !!document.querySelector('[name="astro-view-transitions-enabled"]');
async function y(e) {
    const t = await fetch(e),
        n = await t.text();
    return {
        ok: t.ok,
        html: n
    }
}

function f() {
    const e = document.querySelector('[name="astro-view-transitions-fallback"]');
    return e ? e.getAttribute("content") : "animate"
}
const g = new DOMParser;
async function d(e, t, n) {
    const a = g.parseFromString(t, "text/html");
    a.documentElement.dataset.astroTransition = e;
    const i = () => document.documentElement.replaceWith(a.documentElement),
        c = Array.from(a.querySelectorAll("head link[rel=stylesheet]")).map(r => new Promise(h => {
            const l = r.cloneNode();
            ["load", "error"].forEach(p => l.addEventListener(p, h)), document.head.append(l)
        }));
    if (c.length && await Promise.all(c), n === "animate") {
        let r = !1;
        addEventListener("animationstart", () => r = !0, {
            once: !0
        }), document.documentElement.dataset.astroTransitionFallback = "old", a.documentElement.dataset.astroTransitionFallback = "new", addEventListener("animationend", i, {
            once: !0
        }), setTimeout(() => !r && i())
    } else i()
}
async function u(e, t) {
    let n;
    const {
        html: a,
        ok: i
    } = await y(t);
    if (!i) {
        location.href = t;
        return
    }
    m ? n = document.startViewTransition(() => d(e, a)).finished : n = d(e, a, f());
    try {
        await n
    } finally {
        document.documentElement.removeAttribute("data-astro-transition")
    }
}

function E(e) {
    if (document.querySelector(`link[rel=prefetch][href="${e}"]`)) return;
    if (navigator.connection) {
        let n = navigator.connection;
        if (n.saveData || /(2|3)g/.test(n.effectiveType || "")) return
    }
    let t = document.createElement("link");
    t.setAttribute("rel", "prefetch"), t.setAttribute("href", e), document.head.append(t)
}(m || f() !== "none") && (document.addEventListener("click", e => {
    let t = e.target;
    t instanceof Element && t.tagName !== "A" && (t = t.closest("a")), t && t instanceof HTMLAnchorElement && t.href && (!t.target || t.target === "_self") && t.origin === location.origin && e.button === 0 && !e.metaKey && !e.ctrlKey && !e.altKey && !e.shiftKey && !e.defaultPrevented && s() && (e.preventDefault(), u("forward", t.href), o++, history.pushState({
        index: o
    }, "", t.href))
}), window.addEventListener("popstate", () => {
    if (!s()) {
        location.reload();
        return
    }
    const e = history.state?.index ?? o + 1,
        t = e > o ? "forward" : "back";
    u(t, location.href), o = e
}), ["mouseenter", "touchstart", "focus"].forEach(e => {
    document.addEventListener(e, t => {
        if (t.target instanceof HTMLAnchorElement) {
            let n = t.target;
            n.origin === location.origin && n.pathname !== location.pathname && s() && E(n.pathname)
        }
    }, {
        passive: !0,
        capture: !0
    })
}));
```
