<script setup lang="ts">
import { Menu } from 'lucide-vue-next'
import { Button } from '@/components/ui/button'
import {
    Sheet,
    SheetContent,
    SheetDescription,
    SheetFooter,
    SheetHeader,
    SheetTitle,
    SheetTrigger
} from '@/components/ui/sheet'
import { Tooltip, TooltipContent, TooltipProvider, TooltipTrigger } from '@/components/ui/tooltip'
</script>

<template>
    <TooltipProvider>
        <Tooltip>
            <TooltipTrigger>
                <Sheet>
                    <SheetTrigger>
                        <Button variant="outline" size="icon">
                            <Menu class="h-[1.2rem] w-[1.2rem]" />
                        </Button>
                    </SheetTrigger>
                    <SheetContent>
                        <SheetHeader>
                            <SheetTitle>Menu</SheetTitle>
                            <SheetDescription>
                                Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
                                eiusmod tempor incididunt ut labore et dolore magna aliqua.
                            </SheetDescription>
                        </SheetHeader>
                        <SheetFooter> </SheetFooter>
                    </SheetContent>
                </Sheet>
            </TooltipTrigger>
            <TooltipContent>
                <p>Menu</p>
            </TooltipContent>
        </Tooltip>
    </TooltipProvider>
</template>
