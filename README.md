# Priority card fixes

Below I will attach URLs with lines from GitHub with the code that should be replaced

### Fixes for the function ``baseFilteredItems``

On the ``Cards`` component from the line ``237`` to line ``266`` replace the [current function](https://github.com/Spunkie/monday.com-cro-solution/blob/e70c9e7ac5d73bfcf6748646a2aa3becca038de1/packages/components/src/cards/Cards.vue#L237-L266) with the new one:

	baseFilteredItems({ items, boardSettings: { buttonsshowAll, sortScore } }) {
		if (items.length === 0 || this.$_isEmptyObject(this.boardSettings)) {
			return []
		}
		let filteredItems = items.filter((item) => {
			item.score = this.parseItemFormula(item)
			item.complete = this.parseItemProgress(item)

			if (buttonsshowAll === `all`) {
				return item
			}
			if (buttonsshowAll === `prioritized` && item.complete) {
				return item
			}
			if (buttonsshowAll === `unprioritized` && !item.complete) {
				return item
			}
		})

		if (sortScore === `ascending`) {
			filteredItems = filteredItems.sort((a, b) => {
				return this.getNumbersColumnValue(a) - this.getNumbersColumnValue(b)
			})
		} else if (sortScore === `descending`) {
			filteredItems = filteredItems.sort((a, b) => {
				return this.getNumbersColumnValue(b) - this.getNumbersColumnValue(a)
			})
		}
		return filteredItems
	}

### Fixing the deadline undefined error

On the ``ClockIcon`` component from the line ``48`` to line ``68`` replace the [current function](https://github.com/Spunkie/monday.com-cro-solution/blob/e70c9e7ac5d73bfcf6748646a2aa3becca038de1/packages/components/src/cards/ClockIcon.vue#L49-L68) with the new one:

	tooltip({ deadline, done, $_tooltipClasses }) {
		if (deadline) {
			const date = new Date(deadline)
			const currentYear = new Date().getFullYear()
			const deadlineYear = date.getFullYear()
			const displayYear = currentYear !== deadlineYear ? `numeric` : undefined

			const dateTimeFormat = new Intl.DateTimeFormat(`en`, {
				year: displayYear,
				month: `short`,
				day: `2-digit`,
			})

			const parts = dateTimeFormat.formatToParts(date)
			const formattedDate = parts
				.map(({ type, value }) =>
					type === `literal` ? value : `<span class="${type}">${value}</span>`
				)
				.join(``)
			if (done) {
				return {
					content: `<span class="strikethrough">${formattedDate}</span>`,
					classes: $_tooltipClasses,
				}
			}
			return { content: `${formattedDate}`, classes: $_tooltipClasses }
		}
		return {}
	},

### Displaying the ``item.score`` based on the new structure

On the ``Card`` component replce the line ``37``([url](https://github.com/Spunkie/monday.com-cro-solution/blob/e70c9e7ac5d73bfcf6748646a2aa3becca038de1/packages/components/src/cards/Card.vue#L37)) with the new one:
	
	<div> {{ item.score }} </div>

On the same component add a new ``function`` inside the ``computed``, see the code below

	numbersColumnValue() {
		const numbersColumn = this.item.column_values.find((column) => column.id === `numbers`)
		return numbersColumn ? numbersColumn.text : `N/A`
	},
