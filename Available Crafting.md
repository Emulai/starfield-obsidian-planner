Resources:: [[Aluminium]], [[Beryllium]], [[Helium-3]], [[Argon]], [[Iron]], [[Water]], [[Lead]], [[Tungsten]], [[Titanium]]

```dataviewjs
function sort(a, b) {
	var nameA = a.file.name.toUpperCase();
	var nameB = b.file.name.toUpperCase();
	return (nameA < nameB) ? -1 : (nameA > nameB) ? 1 : 0;
}

function getType(folder) {
	const pathSections = folder.split('/');
	return pathSections[pathSections.length - 1];
}

const searchedResources = dv.current().resources.map(r => {
	return dv.page(r.path).file.name;
});

const allCraftables = [
	...dv.pages('"resources/fabricated"'), 
	...dv.pages('"outposts"')
];

const availableCraftables = allCraftables.filter(c => {
	const craftable = dv.page(c.file.path);
	if (!craftable.resource) return true;
	if (!Array.isArray(craftable.resource)) return true;

	const resources = craftable.resource.map(r => {
		const resource = dv.page(r.path);
		return resource.file.name;
	});
	const isAvailable = resources.every(r => searchedResources.includes(r));
	
	return isAvailable;
});

dv.table(
	['Name', 'Resources', 'Type'],
	availableCraftables.sort(sort).map(ac => {
	if (!ac.resource)
		return [ac.file.name, '', getType(ac.file.folder)];
	if (!Array.isArray(ac.resource)) 
		return [ac.file.name, ac.resource, getType(ac.file.folder)];
	
	return [
		ac.file.name,
		ac.resource.join(', '),
		getType(ac.file.folder)
	];
}));

dv.table(
	['Name'],
	allCraftables.sort(sort).filter(ac => {
		if (ac.research) return true;
		if (ac.resources) return true;
		return false;
	}).map(ac => [ac.file.name]));
```
