# Webdev: What I like in 2024

## React

The trifecta of React, Next.js, and Typescript are the dominant defaults today.
React itself has been around for a decade now, I don't see it going away.

The underappreciated brilliance of React is that it makes webdev like gamedev,
in that the world state changes and then the screen gets rerendered. All of the
complexity in React (eg, the virtual DOM) exists to make this gamedev-like API
run fast in the browser.

I like this hook:

```ts
type ChangeValueHandler = ChangeEventHandler<
  HTMLSelectElement | HTMLInputElement // TODO: support all input elements
>;

function useInputState(defaultValue: string) {
  const [value, setValue] = useState(defaultValue);
  const changeValue: ChangeValueHandler = (evt) => setValue(evt.target.value);
  return [value, changeValue, setValue] as const;
}

function ExampleComponent() {
  const [foo, changeFoo] = useInputState("foo");

  return <input type="text" value={foo} onChange={changeFoo} />;
}
```

## Next.js

Unclear whether Next.js will be as long-lived as React, but at least they seem
to have built a sustainable business on it. (The downside is that Vercel can be
pretty expensive; I prefer Render (see below).)

Next.js has achieved what JavaScript folks have all dreamed of for years: the
ability to run the same code on your server and client, prerendering pages on
the server and rendering them locally. I don't love Next.js aesthetically, it
feels a lot like they just made all their required features work and then added
the necessary escape hatches. But it does work, and that is an accomplishment.

In practice the biggest benefit of Next.js is that with one `create-react-app`
command you get a React app with a fully-configured compiler/bundler and a setup
for both serving the frontend app and also serving API endpoints.

I configure my apps with no `src/` directory — the git repo is the source code,
so what's the point? They just have a top-level `app/` directory for the page
files, and a `lib/` directory for helpers and components shared across pages.
Components that are not shared across pages live in the directory alongside
`page.tsx`.

## TypeScript

TypeScript brilliantly brought a worse-is-better approach to a strongly typed
programming language. It uses runtime checks for type refinement, which really
bridges the dynamic typing and static typechecking styles:

```ts
const x = stringOrUndefined();
if (typeof x === "undefined") {
  return;
}
// here tsc knows that x is a string
```

## Deploying: Render

Render is everything I hoped for in a Heroku successor. I use it to host Next.js
apps, Python API endpoints, and for hobby projects they have a very reasonable
Postgres deployment you can use. None of the enterprise complexity of AWS or GCP.

Caveat: I haven't yet tried to migrate off of Render to a "grown-up" cloud
provider like AWS or GCP.

## Styling: Panda

Panda lets you write CSS in JS like so:

```ts
function ExampleComponent() {
  return <div className={css({ color: "#31448a" })}>Example</div>;
}
```

But the cool part is that it puts your styles into a static CSS file and
extracts shared styles across components.

It also works with server-side rendering.

I don't like Tailwind or Chakra which make me learn a whole new system atop.
Frequently with my personal projects I'm trying weird styles, not the normal
landing pages with hero headers and apps with buttons and forms, so I don't
really get the benefit of chose systems. Plus, I already know CSS pretty well.
With those systems I end up almost immediately reaching for the escape hatches.
Panda (which was created in order to build the next version of Chakra) is the
right level of abstraction for me.

## Database: PostgreSQL

It's always been great, and now it's widely available (eg on Render). It has a
lot of features, performs well, and is well-documented.

## What I still want

A good database library. I don't want a full ORM/query language — like with CSS,
extra conceptual layers tend to just be confusing. But I do want easy mapping
from rows to objects and back.

On the other hand, I often end up implementing the "real" API backend in Go. And
the API backend doesn't need to be in JavaScript, because it's also serving
Swift and Kotlin frontends.

On the note of API backends, I also want an open-source user framework. Stuff
like user creation, management, password reset, etc. NextAuth exists but is not
as popular as I'd expect. Startups like Clerk provide this as a service but I
don't want to hand over my user table.
