
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TEXT 512
#define MAX_LINKS 10

typedef struct Block {
    char id[32];
    char content[MAX_TEXT];
} Block;

typedef struct Link {
    char source_id[32];
    char target_id[32];
} Link;

typedef struct IEX_Engine {
    Block blocks[20];
    int block_count;

    Link links[50];
    int link_count;
} IEX_Engine;

void add_two_way_link(IEX_Engine *engine, const char *src_id, const char *tgt_id) {
    strcpy(engine->links[engine->link_count].source_id, src_id);
    strcpy(engine->links[engine->link_count].target_id, tgt_id);
    engine->link_count++;

    printf("[IEX Linker] Connected (%s) <---> (%s) successfully.\n", src_id, tgt_id);
}

Block* find_block(IEX_Engine *engine, const char *id) {
    for (int i = 0; i < engine->block_count; i++) {
        if (strcmp(engine->blocks[i].id, id) == 0) {
            return &engine->blocks[i];
        }
    }
    return NULL;
}

void render_transcluded_block(IEX_Engine *engine, const char *block_id) {
    Block *b = find_block(engine, block_id);
    if (!b) {
        printf("Error: 404 Block Not Found!\n");
        return;
    }

    printf("\n=== Rendering Block [%s] ===\n", b->id);
    printf("Content: %s\n", b->content);

    printf("--- Backlinks (Who links to this?) ---\n");
    for (int i = 0; i < engine->link_count; i++) {
        if (strcmp(engine->links[i].target_id, block_id) == 0) {
            printf(" <- Referenced by: [%s]\n", engine->links[i].source_id);
        }
    }
    printf("=====================================\n\n");
}

int main() {
    IEX_Engine engine = { .block_count = 0, .link_count = 0 };

    strcpy(engine.blocks[0].id, "quote_01");
    strcpy(engine.blocks[0].content, "Xanadu was not a failed dream, just an unfinished architecture.");
    engine.block_count++;

    strcpy(engine.blocks[1].id, "doc_main");
    strcpy(engine.blocks[1].content, "As stated in [[quote_01]], we are rebuilding it now.");
    engine.block_count++;

    add_two_way_link(&engine, "doc_main", "quote_01");

    render_transcluded_block(&engine, "quote_01");

    return 0;
}
