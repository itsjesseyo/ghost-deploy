# ghost-deploy
my basic ghost deploy for digital ocean

follow this : https://medium.com/@razagill/deploy-ghost-blog-using-docker-and-caddy-461b9b2ff095

but with these variations

base directories are /opt , /opt/data, ./opt/caddy

mkdir -p /opt/data
mkdir -p /opt/caddy/.caddy

docker run -d --restart=always -p 8080:2368 --name ghost --env NODE_ENV=production -v /opt/data/ghost:/var/lib/ghost/content ghost:latest

docker run -d --restart=always -p 80:80 -p 443:443 --name caddy --link ghost:ghost -v /opt/caddy/Caddyfile:/etc/Caddyfile -v /opt/caddy/.caddy:/root/.caddy abiosoft/caddy:latest

USER_DOMAIN_ADDRESS {  
    proxy /ghost:2368 {
        header_upstream Host {host}
        header_upstream X-Real-IP {remote}
        header_upstream X-Forwarded-Proto {scheme}
    }
    tls USER_EMAIL
}

// # Ghost Configuration
// Setup your Ghost install for various environments
// Documentation can be found at http://support.ghost.org/config/
 
var path = require('path'),
config;

config = {
    // ### Production
    // When running Ghost in the wild, use the production environment
    // Configure your URL and mail settings here
production: {  
    url: 'http://YOUR_DOMAIN_NAME',
    mail: {
        transport: 'SMTP',
        options: {
            service: 'Mailgun',
            auth: {
                user: 'YOUR_EMAIL',
                pass: 'YOUR_PASSWORD'
            }
        }
    },
    database: {
        client: 'sqlite3',
        connection: {
            filename: path.join(process.env.GHOST_CONTENT, '/data/ghost.db')
        },
        debug: false
    },
server: {
        host: '0.0.0.0',
        port: '2368'
    },
paths: {
        contentPath: path.join(process.env.GHOST_CONTENT, '/')
    }
},

    // ### Development **(default)**
        development: {
            // The url to use when providing links to the site, E.g. in RSS and email.
            // Change this to your Ghost blogs published URL.
            url: 'http://localhost:2368',

            // Example mail config
            // Visit http://support.ghost.org/mail for instructions
            // ```
            //  mail: {
           //      transport: 'SMTP',
            //      options: {
            //          service: 'Mailgun',
            //          auth: {
            //              user: '', // mailgun username
            //              pass: ''  // mailgun password
            //          }
            //      }
            //  },
            // ```

            database: {
                client: 'sqlite3',
                connection: {
                    filename: path.join(__dirname, '/content/data/ghost-dev.db')
                },
                debug: false
            },
            server: {
                // Host to be passed to node's `net.Server#listen()`
                host: '127.0.0.1',
                // Port to be passed to node's `net.Server#listen()`, for iisnode set this to `process.env.PORT`
                port: '2368'
           },
            paths: {
                contentPath: path.join(__dirname, '/content/')
            }
        },

    // **Developers only need to edit below here**

    // ### Testing
    // Used when developing Ghost to run tests and check the health of Ghost
    // Uses a different port number
    testing: {
        url: 'http://127.0.0.1:2369',
        database: {
            client: 'sqlite3',
            connection: {
                filename: path.join(__dirname, '/content/data/ghost-test.db')
            }
        },
        server: {
            host: '127.0.0.1',
            port: '2369'
        },
        logging: false
    },

    // ### Testing MySQL
    // Used by Travis - Automated testing run through GitHub
    'testing-mysql': {
        url: 'http://127.0.0.1:2369',
        database: {
            client: 'mysql',
            connection: {
                host     : '127.0.0.1',
                user     : 'root',
                password : '',
                database : 'ghost_testing',
                charset  : 'utf8'
            }
        },
        server: {
            host: '127.0.0.1',
            port: '2369'
        },
        logging: false
    },

    // ### Testing pg
    // Used by Travis - Automated testing run through GitHub
    'testing-pg': {
        url: 'http://127.0.0.1:2369',
        database: {
            client: 'pg',
            connection: {
                host     : '127.0.0.1',
                user     : 'postgres',
                password : '',
                database : 'ghost_testing',
                charset  : 'utf8'
            }
        },
        server: {
            host: '127.0.0.1',
            port: '2369'
        },
        logging: false
    }
};

// Export config
module.exports = config;
