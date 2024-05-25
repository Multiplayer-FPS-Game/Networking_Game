#include "yojimbo.h"
#include <signal.h>
#include <time.h>

#include "shared.h"

using namespace yojimbo;

static volatile int quit = 0;

void interrupt_handler( int /*dummy*/ )
{
    quit = 1;
}

int ServerMain()
{
    printf( "started server on port %d (insecure)\n", ServerPort );

    double time = 100.0;

    ClientServerConfig config;

    uint8_t privateKey[KeyBytes];
    memset( privateKey, 0, KeyBytes );

    Server server( GetDefaultAllocator(), privateKey, Address( "127.0.0.1", ServerPort ), config, adapter, time );

    server.Start( MaxClients );

    char addressString[256];
    server.GetAddress().ToString( addressString, sizeof( addressString ) );
    printf( "server address is %s\n", addressString );

    const double deltaTime = 0.01f;

    signal( SIGINT, interrupt_handler );    

    while ( !quit )
    {
        server.SendPackets();

        server.ReceivePackets();

        time += deltaTime;

        server.AdvanceTime( time );

        if ( !server.IsRunning() )
            break;

        yojimbo_sleep( deltaTime );
    }

    server.Stop();

    return 0;
}

int main()
{
    printf( "\n" );

    if ( !InitializeYojimbo() )
    {
        printf( "error: failed to initialize Yojimbo!\n" );
        return 1;
    }

    yojimbo_log_level( YOJIMBO_LOG_LEVEL_INFO );

    srand( (unsigned int) time( NULL ) );

    int result = ServerMain();

    ShutdownYojimbo();

    printf( "\n" );

    return result;
}
