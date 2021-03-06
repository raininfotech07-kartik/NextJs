--------------------------------------------------------------------------------------------------------------------------------------------------------------------
                                                          Session
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
    var session = require('express-session');
    var FileStore = require('session-file-store')(session);
    var fileStoreOptions = {};
    import nextSession from "next-session";
    import { promisifyStore } from "next-session/lib/compat";
    const connectStore = new FileStore(fileStoreOptions);
    const getSession = nextSession({
        store: promisifyStore(connectStore),
        secret: 'keyboard cat',
        resave: true,
        saveUninitialized: true,
    });
    export async function getServerSideProps({ req, res }) {
        const session = await getSession(req, res);
        session.views = session.views ? session.views + 1 : 1;
    })

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
                                                New file access after build
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
    => Files // for file storage
    => Pages/_middleware.js // for rewrite url
        import { NextResponse } from 'next/server'
        export function middleware(request) {
          const url = request.nextUrl.clone()
          if((request.nextUrl.pathname).includes('/stuff/')){
            url.pathname = '/api' + request.nextUrl.pathname
            return NextResponse.rewrite(url)
          }
        }

    => Pages/api/stuff/[...slug].js // for access images
        export const config = {
            api: { externalResolver: true }
        }
        import express from 'express';
        const handler = express();
        const serveFiles = express.static('public');
        handler.use('/stuff', serveFiles);
        export default handler;

    => Load this url : http://localhost:3000/images/forgot_password.png
