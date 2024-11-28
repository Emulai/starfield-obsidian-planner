Goal:: [[Landing Pad with Shipbuilder]]

```dataviewjs
const goalIn = dv.current().goal;

let goals = [];
if (!goalIn) goals = [];
else if (!Array.isArray(goalIn)) 
	goals.push(dv.page(goalIn.path));
else goals = dv.current().goal.map(g => dv.page(g.path));

const entries = [];
const totalResources = {};

function sort(a, b) {
    var nameA = a.name.toUpperCase();
    var nameB = b.name.toUpperCase();
    return (nameA < nameB) ? -1 : (nameA > nameB) ? 1 : 0;
}

function recurseResources(component, parentName, multiple) {
	if (component.resource) {
		component.resource.forEach((resource, index) => {
			const resourceAmount = component.amount[index];
			const resourceMultiple = resourceAmount * multiple;
			const resourceDetail = dv.page(resource.path);
			const entryName = `${resourceDetail.file.name}`;
			const label = `: x${resourceAmount}`;

			const intto = 
				`(${parentName}) <|-- (${entryName})${label}\n`;
			entries.push(intto);

			if (!totalResources[entryName]) totalResources[entryName] = resourceMultiple;
			else totalResources[entryName] += resourceMultiple;

			recurseResources(resourceDetail, entryName, resourceMultiple);
		});
	}
}

goals.forEach(g => {
	entries.push(`(${g.file.name}) <<top>>`);
	recurseResources(g, `${g.file.name}`, 1);
});

const layout = [...new Set(entries)].join('\n');
let graph = '```plantuml\n';
graph += `
hide stereotype
<style>
!$BGCOLOR = "#1e1e1e"
!$FGCOLOR = "#dadada"
!$HIGHLGT = "#a882ff"
root {
	BackgroundColor $BGCOLOR
	FontColor $FGCOLOR
	LineColor $FGCOLOR
}

.top {
	FontColor $HIGHLGT
	LineColor $HIGHLGT
}
</style>
`;
graph += layout;

const resourceArray = Object.entries(totalResources).map(r => {
	return {
		name: r[0],
		amount: r[1]
	};
});

graph += '```';
console.log(graph);
dv.paragraph(graph);
dv.table(
	['Resource', 'Total #'],
	resourceArray.sort(sort).map(r => {
	return [r.name, r.amount];
}));
```

