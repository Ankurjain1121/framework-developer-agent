# Component Mapping Reference

UX patterns mapped to shadcn/ui components with composition examples.

---

## Navigation Patterns

### Top Navigation Bar
```tsx
// Components: NavigationMenu, Button, DropdownMenu, Avatar
<header className="border-b">
  <nav className="flex items-center justify-between h-14 px-4">
    <NavigationMenu>
      <NavigationMenuList>
        <NavigationMenuItem>
          <NavigationMenuLink>Dashboard</NavigationMenuLink>
        </NavigationMenuItem>
      </NavigationMenuList>
    </NavigationMenu>

    <div className="flex items-center gap-2">
      <Button variant="ghost" size="icon">
        <Bell className="h-4 w-4" />
      </Button>
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <Avatar className="h-8 w-8 cursor-pointer" />
        </DropdownMenuTrigger>
        <DropdownMenuContent align="end">
          <DropdownMenuItem>Profile</DropdownMenuItem>
          <DropdownMenuItem>Settings</DropdownMenuItem>
          <DropdownMenuSeparator />
          <DropdownMenuItem>Log out</DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
    </div>
  </nav>
</header>
```

### Sidebar Navigation
```tsx
// Components: Button, Tooltip, Separator
<aside className="w-64 border-r h-screen flex flex-col">
  <div className="p-4">
    <Logo />
  </div>

  <nav className="flex-1 px-2 space-y-1">
    {navItems.map(item => (
      <Button
        key={item.href}
        variant={active ? 'secondary' : 'ghost'}
        className="w-full justify-start"
        asChild
      >
        <Link href={item.href}>
          <item.icon className="h-4 w-4 mr-2" />
          {item.label}
        </Link>
      </Button>
    ))}
  </nav>

  <Separator />

  <div className="p-4">
    <UserMenu />
  </div>
</aside>
```

### Breadcrumbs
```tsx
// Components: Breadcrumb
<Breadcrumb>
  <BreadcrumbList>
    <BreadcrumbItem>
      <BreadcrumbLink href="/">Home</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbLink href="/projects">Projects</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbPage>Current Project</BreadcrumbPage>
    </BreadcrumbItem>
  </BreadcrumbList>
</Breadcrumb>
```

---

## Data Display Patterns

### Data Table
```tsx
// Components: Table, Button, DropdownMenu, Checkbox, Badge
<Table>
  <TableHeader>
    <TableRow>
      <TableHead className="w-12">
        <Checkbox />
      </TableHead>
      <TableHead>Name</TableHead>
      <TableHead>Status</TableHead>
      <TableHead className="w-12" />
    </TableRow>
  </TableHeader>
  <TableBody>
    {data.map(row => (
      <TableRow key={row.id}>
        <TableCell>
          <Checkbox />
        </TableCell>
        <TableCell className="font-medium">{row.name}</TableCell>
        <TableCell>
          <Badge variant={row.status === 'active' ? 'default' : 'secondary'}>
            {row.status}
          </Badge>
        </TableCell>
        <TableCell>
          <DropdownMenu>
            <DropdownMenuTrigger asChild>
              <Button variant="ghost" size="icon">
                <MoreHorizontal className="h-4 w-4" />
              </Button>
            </DropdownMenuTrigger>
            <DropdownMenuContent align="end">
              <DropdownMenuItem>Edit</DropdownMenuItem>
              <DropdownMenuItem className="text-destructive">Delete</DropdownMenuItem>
            </DropdownMenuContent>
          </DropdownMenu>
        </TableCell>
      </TableRow>
    ))}
  </TableBody>
</Table>
```

### Card Grid
```tsx
// Components: Card, Badge, Button, Avatar
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => (
    <Card key={item.id}>
      <CardHeader className="flex flex-row items-center gap-4">
        <Avatar>
          <AvatarImage src={item.image} />
          <AvatarFallback>{item.initials}</AvatarFallback>
        </Avatar>
        <div>
          <CardTitle className="text-base">{item.title}</CardTitle>
          <CardDescription>{item.subtitle}</CardDescription>
        </div>
      </CardHeader>
      <CardContent>
        <p className="text-sm text-muted-foreground">{item.description}</p>
      </CardContent>
      <CardFooter className="justify-between">
        <Badge>{item.category}</Badge>
        <Button variant="ghost" size="sm">View</Button>
      </CardFooter>
    </Card>
  ))}
</div>
```

### List View
```tsx
// Components: Card, Separator
<Card>
  {items.map((item, index) => (
    <Fragment key={item.id}>
      <div className="flex items-center justify-between p-4">
        <div className="flex items-center gap-3">
          <div className="h-10 w-10 rounded bg-muted flex items-center justify-center">
            <item.icon className="h-5 w-5 text-muted-foreground" />
          </div>
          <div>
            <p className="font-medium">{item.title}</p>
            <p className="text-sm text-muted-foreground">{item.description}</p>
          </div>
        </div>
        <Button variant="outline" size="sm">Action</Button>
      </div>
      {index < items.length - 1 && <Separator />}
    </Fragment>
  ))}
</Card>
```

---

## Form Patterns

### Standard Form
```tsx
// Components: Form, Input, Select, Textarea, Button
<Form {...form}>
  <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
    <FormField
      control={form.control}
      name="name"
      render={({ field }) => (
        <FormItem>
          <FormLabel>Name</FormLabel>
          <FormControl>
            <Input {...field} />
          </FormControl>
          <FormMessage />
        </FormItem>
      )}
    />

    <FormField
      control={form.control}
      name="category"
      render={({ field }) => (
        <FormItem>
          <FormLabel>Category</FormLabel>
          <Select onValueChange={field.onChange} defaultValue={field.value}>
            <FormControl>
              <SelectTrigger>
                <SelectValue placeholder="Select category" />
              </SelectTrigger>
            </FormControl>
            <SelectContent>
              <SelectItem value="a">Category A</SelectItem>
              <SelectItem value="b">Category B</SelectItem>
            </SelectContent>
          </Select>
          <FormMessage />
        </FormItem>
      )}
    />

    <Button type="submit">Submit</Button>
  </form>
</Form>
```

### Search with Filters
```tsx
// Components: Input, Select, Button, Popover
<div className="flex items-center gap-2">
  <div className="relative flex-1">
    <Search className="absolute left-2.5 top-2.5 h-4 w-4 text-muted-foreground" />
    <Input
      placeholder="Search..."
      className="pl-8"
      value={search}
      onChange={e => setSearch(e.target.value)}
    />
  </div>

  <Select value={status} onValueChange={setStatus}>
    <SelectTrigger className="w-[150px]">
      <SelectValue placeholder="Status" />
    </SelectTrigger>
    <SelectContent>
      <SelectItem value="all">All Status</SelectItem>
      <SelectItem value="active">Active</SelectItem>
      <SelectItem value="inactive">Inactive</SelectItem>
    </SelectContent>
  </Select>

  <Popover>
    <PopoverTrigger asChild>
      <Button variant="outline" size="icon">
        <Filter className="h-4 w-4" />
      </Button>
    </PopoverTrigger>
    <PopoverContent className="w-80">
      {/* Advanced filters */}
    </PopoverContent>
  </Popover>
</div>
```

---

## Modal Patterns

### Confirmation Dialog
```tsx
// Components: AlertDialog
<AlertDialog>
  <AlertDialogTrigger asChild>
    <Button variant="destructive">Delete</Button>
  </AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogHeader>
      <AlertDialogTitle>Are you sure?</AlertDialogTitle>
      <AlertDialogDescription>
        This action cannot be undone. This will permanently delete the item.
      </AlertDialogDescription>
    </AlertDialogHeader>
    <AlertDialogFooter>
      <AlertDialogCancel>Cancel</AlertDialogCancel>
      <AlertDialogAction onClick={handleDelete}>Delete</AlertDialogAction>
    </AlertDialogFooter>
  </AlertDialogContent>
</AlertDialog>
```

### Form Dialog
```tsx
// Components: Dialog, Form
<Dialog open={open} onOpenChange={setOpen}>
  <DialogTrigger asChild>
    <Button>Create New</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Create Item</DialogTitle>
      <DialogDescription>Add a new item to your collection.</DialogDescription>
    </DialogHeader>
    <CreateForm onSuccess={() => setOpen(false)} />
  </DialogContent>
</Dialog>
```

### Sheet (Side Panel)
```tsx
// Components: Sheet
<Sheet>
  <SheetTrigger asChild>
    <Button variant="outline">Open Details</Button>
  </SheetTrigger>
  <SheetContent>
    <SheetHeader>
      <SheetTitle>Item Details</SheetTitle>
      <SheetDescription>View and edit item information.</SheetDescription>
    </SheetHeader>
    <div className="py-4">
      {/* Content */}
    </div>
    <SheetFooter>
      <Button>Save Changes</Button>
    </SheetFooter>
  </SheetContent>
</Sheet>
```

---

## Feedback Patterns

### Toast Notifications
```tsx
// Using sonner (recommended) or shadcn toast
import { toast } from 'sonner'

// Success
toast.success('Changes saved')

// Error
toast.error('Failed to save changes')

// With action
toast('Item deleted', {
  action: {
    label: 'Undo',
    onClick: () => handleUndo(),
  },
})
```

### Inline Alerts
```tsx
// Components: Alert
<Alert>
  <Info className="h-4 w-4" />
  <AlertTitle>Note</AlertTitle>
  <AlertDescription>This is an informational message.</AlertDescription>
</Alert>

<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>Error</AlertTitle>
  <AlertDescription>{errorMessage}</AlertDescription>
</Alert>
```

---

## Selection Patterns

### Command (Searchable Select)
```tsx
// Components: Command, Popover
<Popover open={open} onOpenChange={setOpen}>
  <PopoverTrigger asChild>
    <Button variant="outline" className="w-[200px] justify-between">
      {value ? items.find(i => i.value === value)?.label : 'Select...'}
      <ChevronsUpDown className="ml-2 h-4 w-4 opacity-50" />
    </Button>
  </PopoverTrigger>
  <PopoverContent className="w-[200px] p-0">
    <Command>
      <CommandInput placeholder="Search..." />
      <CommandEmpty>No results found.</CommandEmpty>
      <CommandGroup>
        {items.map(item => (
          <CommandItem
            key={item.value}
            value={item.value}
            onSelect={v => { setValue(v); setOpen(false) }}
          >
            <Check className={cn('mr-2 h-4 w-4', value === item.value ? 'opacity-100' : 'opacity-0')} />
            {item.label}
          </CommandItem>
        ))}
      </CommandGroup>
    </Command>
  </PopoverContent>
</Popover>
```

### Tabs
```tsx
// Components: Tabs
<Tabs defaultValue="overview" className="w-full">
  <TabsList>
    <TabsTrigger value="overview">Overview</TabsTrigger>
    <TabsTrigger value="analytics">Analytics</TabsTrigger>
    <TabsTrigger value="settings">Settings</TabsTrigger>
  </TabsList>
  <TabsContent value="overview">
    <OverviewPanel />
  </TabsContent>
  <TabsContent value="analytics">
    <AnalyticsPanel />
  </TabsContent>
  <TabsContent value="settings">
    <SettingsPanel />
  </TabsContent>
</Tabs>
```

---

## Composition Patterns

### Page Header
```tsx
<div className="flex items-center justify-between">
  <div>
    <h1 className="text-2xl font-semibold tracking-tight">Page Title</h1>
    <p className="text-muted-foreground">Page description or subtitle.</p>
  </div>
  <div className="flex items-center gap-2">
    <Button variant="outline">Secondary</Button>
    <Button>Primary Action</Button>
  </div>
</div>
```

### Stats Cards
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  {stats.map(stat => (
    <Card key={stat.name}>
      <CardHeader className="flex flex-row items-center justify-between pb-2">
        <CardTitle className="text-sm font-medium text-muted-foreground">
          {stat.name}
        </CardTitle>
        <stat.icon className="h-4 w-4 text-muted-foreground" />
      </CardHeader>
      <CardContent>
        <div className="text-2xl font-bold">{stat.value}</div>
        <p className="text-xs text-muted-foreground">
          {stat.change} from last month
        </p>
      </CardContent>
    </Card>
  ))}
</div>
```

### Empty State with Action
```tsx
<div className="flex flex-col items-center justify-center py-12 text-center">
  <div className="rounded-full bg-muted p-3 mb-4">
    <Inbox className="h-6 w-6 text-muted-foreground" />
  </div>
  <h3 className="font-semibold">No items yet</h3>
  <p className="text-sm text-muted-foreground mt-1 max-w-sm">
    Get started by creating your first item.
  </p>
  <Button className="mt-4">
    <Plus className="h-4 w-4 mr-2" />
    Create Item
  </Button>
</div>
```
