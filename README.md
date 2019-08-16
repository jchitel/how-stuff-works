# How Stuff Works

I enjoy doing deep dives when I'm working with projects that I really like, primarily because I find something *ever so slightly* wrong with them and become obsessed with finding out *why* it wasn't designed the way that *I* would have done it. Typically, the result of this deep diving is one of two things:

1. I discover one or more good reasons that it was done that way, allowing me to move on with my life
2. I discover one or more bad reasons that it was done that way, giving me free reign to redesign the solution the "right" way

Regardless of what happens, I discover things. And I'd like to start recording these discoveries in a central repository that I'll eventually publish in a more easy-to-consume location (such as a personal website). That's where this repo comes in.

I generally start by simply taking notes on all of the workflows of the project, starting with the entry point. This might be a CLI command, the main function of an executable, or in the case of a library, the API. Going through this process walks me through how the data flows in the project, shedding light on the architecture and internal function of the project.

Once I've gone through all that, I have a pretty solid understanding of "how it works", so I can start writing actual documentation, split into various chapters explaining core components and workflows in a more digestible way.

Each project has its own directory in this repository, containing:
* A `README.md` to serve as the root of the documentation, containing information about the project, its use cases, the specific version of the project being documented, and a table of contents for the documentation.
* Adjacent documents linked to from the main README that document the project. These may be organized into further subdirectories for large or complex projects.
* A _notes/ directory containing my initial notes (not necessary to read, but it's there for posterity).

## Current Projects Documented

* [Next.js](./next.js/README.md) ([repo](https://github.com/zeit/next.js)) (IN PROGRESS)

## Future Projects I Want To Document

* [React](https://github.com/facebook/react)
* [Webpack](https://github.com/webpack/webpack)
* [Electron](https://github.com/electron/electron)
* [Parcel](https://github.com/parcel-bundler/parcel) (once it hits 2.0)
* [Node.js](https://github.com/nodejs/node)
* [TypeScript](https://github.com/microsoft/typescript)
