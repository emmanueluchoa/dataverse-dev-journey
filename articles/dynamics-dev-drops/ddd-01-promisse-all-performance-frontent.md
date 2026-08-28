<img width="1024" height="572" alt="image" src="https://github.com/user-attachments/assets/d6c11385-1843-44f1-989f-f87168db98aa" />

# Accelerate Dynamics 365: How Promise.all eliminates front-end bottlenecks

> **Dynamics Dev Drops** — a content series with practical Dynamics 365 development tips

### 🚀 Less waiting, more performance: How `Promise.all` improves the experience in Dynamics 365 and Power Apps

During a recent code review, I came across a common pattern when dealing with asynchronous code in JavaScript: running independent requests sequentially.

When we need to fetch multiple pieces of data on the front-end — such as translations, WebAPI records, or form configurations — the way we execute those calls directly impacts screen speed.

### ❌ The common scenario (sequential execution)

In the example below, each `await` waits for the previous call to finish before starting the next one. This creates the classic waterfall effect:

```js
// Fetching each translation one by one ⏳
const confirmDialog = await Xrm.Navigation.openConfirmDialog({
    text: await CustomApp.getTranslation('ownerDifferenceText'),
    title: await CustomApp.getTranslation('ownerDifferenceTitle'),
    confirmButtonLabel: await CustomApp.getTranslation('confirmButtonLabel'),
    cancelButtonLabel: await CustomApp.getTranslation('cancelButtonLabel')
});
```

If each request takes 200ms, the user waits at least 800ms just to load the modal text.

### ✅ The solution (parallel execution)

Since the translation requests don't depend on each other, we can fire them all at once using `Promise.all`:

```js
// Fire all requests simultaneously ⚡
const [text, title, confirmButtonLabel, cancelButtonLabel] = await Promise.all([
    CustomApp.getTranslation('ownerDifferenceText'),
    CustomApp.getTranslation('ownerDifferenceTitle'),
    CustomApp.getTranslation('confirmButtonLabel'),
    CustomApp.getTranslation('cancelButtonLabel')
]);

// Display the modal with all texts pre-loaded
const confirmDialog = await Xrm.Navigation.openConfirmDialog({
    text,
    title,
    confirmButtonLabel,
    cancelButtonLabel
});
```

Now the waiting time is no longer the **sum** of all requests, but simply the time of the **slowest single request** (~200ms total).

### 💡 What's the practical impact?

Small changes like this can noticeably reduce loading times for modals and forms in Dynamics 365, while making the experience smoother for the end user. One caveat worth remembering: `Promise.all` rejects as soon as any single promise fails, so make sure to handle errors accordingly (or use `Promise.allSettled` when partial failures are acceptable).

Do you often spot similar bottlenecks in your code reviews? How do you handle async requests in your daily workflow?

---

*By Emmanuel Cavalcanti — Senior Dynamics 365 Developer | Power Platform, Dataverse & C# Customizations*

`#DynamicsDevDrops` `#Dynamics365` `#PowerPlatform` `#JavaScript` `#WebDevelopment` `#SoftwareEngineering` `#CleanCode` `#Dataverse`
