# User Stories - {PROJECT_NAME}

## Document Overview

This document contains user stories for {PROJECT_NAME}, which {PROJECT_DESCRIPTION}.

**Related Documentation:**
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Technical architecture and vertical slice design
- **[TESTING_STRATEGY.md](TESTING_STRATEGY.md)**: TDD methodology and test organization
- **[README.md](../README.md)**: Project overview and quick start guide

---

## Using GitHub Issues for User Stories

This project uses GitHub Issues to manage user stories following the **3 C's** model:

### The 3 C's of User Stories

**1. Card** - The GitHub issue itself
- Contains the core story in "As a, I want, So that" format
- Includes acceptance criteria as a checklist
- Uses labels for categorization and tracking
- Links to related issues and PRs

**2. Conversation** - The ongoing dialogue
- Issue comments for clarifying questions
- Technical discussions about implementation
- Design decisions and trade-offs
- Links to external conversations (Slack, meetings, docs)
- Progress updates and status changes

**3. Confirmation** - The acceptance criteria
- Checklist in issue body defines "done"
- Each criterion is specific and testable
- Checked off as completed during implementation
- Validated before closing the issue

### Creating User Stories in GitHub

**Using the Issue Template:**

1. Go to **Issues** → **New Issue**
2. Select **User Story** template
3. Fill in the form:
   - **Story ID**: US-{EPIC}-{NUMBER} format
   - **User Role**: Who needs this capability
   - **I want to**: The capability/feature
   - **So that**: The business value
   - **Acceptance Criteria**: Checklist of testable criteria
   - **Priority, Story Points, Sprint**: Planning details
   - **Dependencies**: Link to related issues (#issue-number)

**Example Issue Title:**
```
[US-WZ-001]: Retrieve All Workzones from OFS
```

**Example Acceptance Criteria (in issue body):**
```markdown
- [ ] System connects to OFS instance using credentials
- [ ] All workzones retrieved via OFS API
- [ ] Response includes all relevant attributes
- [ ] API errors return appropriate error messages
- [ ] Request timeout is configurable
- [ ] All unit tests passing (100% coverage)
- [ ] Integration tests passing
- [ ] Documentation updated
```

### Issue Templates Available

This project provides three GitHub issue templates:

1. **User Story** - For new features and functionality
   - Use the "As a, I want, So that" format
   - Include acceptance criteria checklist
   - Track with story points and sprint assignment

2. **Bug Report** - For defects and issues
   - Detailed reproduction steps
   - Expected vs actual behavior
   - Severity and priority classification
   - Environment details

3. **Technical Debt** - For code quality improvements
   - Code refactoring needs
   - Architecture improvements
   - Testing gaps
   - Documentation updates

### Issue Labels for User Stories

Recommended labels:

- **Type**: `user-story`, `technical-story`, `bug`, `technical-debt`, `spike`
- **Priority**: `priority-high`, `priority-medium`, `priority-low`
- **Severity** (bugs): `severity-critical`, `severity-high`, `severity-medium`, `severity-low`
- **Status**: `needs-estimation`, `needs-triage`, `ready`, `in-progress`, `blocked`, `in-review`
- **Epic**: `epic-extraction`, `epic-transformation`, `epic-loading`, etc.
- **Size**: `size-S`, `size-M`, `size-L`, `size-XL`
- **Sprint**: `sprint-1`, `sprint-2`, etc.

### Linking Stories to Code

**In Pull Requests:**
```markdown
Closes #123 (US-WZ-001: Retrieve Workzones)

## Changes
- Implemented WorkzoneService.fetch_from_ofs()
- Added unit tests (15 tests, 94% coverage)
- Updated API documentation

## Acceptance Criteria Completed
- [x] System connects to OFS instance
- [x] All workzones retrieved via API
- [x] Error handling implemented
- [x] Tests passing
```

**In Commits:**
```bash
git commit -m "feat: implement workzone retrieval from OFS API

Implements US-WZ-001 acceptance criteria:
- OFS client integration
- Workzone data extraction
- Error handling and retry logic

Relates to #123"
```

### Tracking Story Progress

**During Implementation:**
1. Move issue to "In Progress" (or use project board)
2. Update acceptance criteria checkboxes as completed
3. Add comments with progress updates
4. Link related PRs: `Relates to #123`
5. Tag team members for review: `@username`

**Conversation Examples:**

```markdown
## Technical Question
@team-lead Should we use Redis or in-memory cache for this story?
Discussed in Slack: [link to thread]

Decision: Using Redis for production, in-memory for tests.
See [ADR-005](docs/adr/005-caching-strategy.md)
```

```markdown
## Blocker
⚠️ Blocked by external API rate limits.

Temporary workaround: Implemented exponential backoff.
Permanent solution tracked in #456.
```

```markdown
## Progress Update
✅ Completed acceptance criteria 1-3
🔄 Working on criterion 4 (error handling)
On track for Sprint 3 completion
```

### Converting This Document to GitHub Issues

To migrate existing stories from this document to GitHub Issues:

```bash
# Use GitHub CLI to create issues from markdown
gh issue create --title "[US-WZ-001]: Retrieve Workzones" \
  --body-file stories/us-wz-001.md \
  --label "user-story,priority-high,epic-workzones" \
  --assignee "@me"
```

Or use the GitHub web interface and paste the story content into the template form.

### Best Practices

**Do:**
- ✅ Use the issue template for consistency
- ✅ Write clear, specific acceptance criteria
- ✅ Link related issues and PRs
- ✅ Update issue status and checkboxes regularly
- ✅ Use comments for important decisions
- ✅ Reference external conversations
- ✅ Close issues only when all criteria are met

**Don't:**
- ❌ Create issues without acceptance criteria
- ❌ Leave issues in limbo (update or close)
- ❌ Have lengthy discussions only in Slack (link back to issue)
- ❌ Close issues with incomplete work
- ❌ Forget to link PRs to issues

---

## **[OPTIONAL]** Completed Stories Archive

User stories completed in previous sprints can be archived for better organization:

- **Sprint 1** (DATE): [COMPLETED_USER_STORIES_SPRINT_1.md](./COMPLETED_USER_STORIES_SPRINT_1.md) - X stories, Y points
  - Brief description of sprint focus
- **Sprint 2** (DATE): [COMPLETED_USER_STORIES_SPRINT_2.md](./COMPLETED_USER_STORIES_SPRINT_2.md) - X stories, Y points
  - Brief description of sprint focus

**Total Completed**: X stories, Y points across N sprints

---

## User Roles & Personas

### Primary Roles

1. **{Role 1}** - {Role description and responsibilities}
2. **{Role 2}** - {Role description and responsibilities}
3. **{Role 3}** - {Role description and responsibilities}

---

## **[OPTIONAL]** Epic 1: {Epic Name}

{Brief description of the epic and its overall goal}

### US-{ID}-001: {Story Title}

**As a** {user role}
**I want** {capability or feature}
**So that** {business value or benefit}

**Acceptance Criteria:**
- [ ] {Specific, testable criterion 1}
- [ ] {Specific, testable criterion 2}
- [ ] {Specific, testable criterion 3}
- [ ] {Additional criteria as needed}

**Priority:** {High | Medium | Low}
**Story Points:** {1 | 2 | 3 | 5 | 8 | 13}
**Sprint:** {Sprint N | Backlog}
**Status:** {Not Started | In Progress | Completed | Blocked}
**Dependencies:** {US-XXX-YYY, US-AAA-BBB} or None

**Technical Notes:** *(Optional)*
- Implementation considerations
- Technology choices
- Architecture patterns

---

### US-{ID}-002: {Story Title} (Simple Format)

**As a** {user role}
**I want** {capability}
**So that** {benefit}

**Acceptance Criteria:**
- [ ] {Criterion 1}
- [ ] {Criterion 2}

---

### US-{ID}-003: {Story Title} (Detailed Format)

**As a** {user role}
**I want** {capability}
**So that** {benefit}

**Context:** *(Optional)*
{Additional background information or rationale}

**Acceptance Criteria:**
- [ ] **AC1**: {Detailed acceptance criterion with clear pass/fail}
- [ ] **AC2**: {Another detailed criterion}
- [ ] **AC3**: {Performance or quality requirement}

**Technical Details:** *(Optional)*
- **Input**: {What the feature receives}
- **Output**: {What the feature produces}
- **API/Interface**: {External interfaces}
- **Configuration**: {Configuration requirements}
- **Dependencies**: {External systems or services}

**Test Strategy:** *(Optional)*
- Unit: {Number and focus of unit tests}
- Integration: {Integration test requirements}
- E2E: {End-to-end test scenarios}

**Priority:** {High | Medium | Low}
**Sprint:** {Sprint N}
**Dependencies:** {US-XXX-YYY}
**Story Points:** {points}
**Status:** ✅ {Status} ({Date})
**Refinement Document:** *(Optional)* `docs/refinement/STORY_US_{ID}_003_REFINEMENT_{DATE}.md`

---

### US-{ID}-004: {Archived Story} ✅

**Status:** Completed in Sprint {N} - See [COMPLETED_USER_STORIES_SPRINT_{N}.md](./COMPLETED_USER_STORIES_SPRINT_{N}.md)

---

## **[OPTIONAL]** Epic 2: {Another Epic}

{Epic description}

### US-{ID}-005: {Story Title}

**As a** {role}
**I want** {capability}
**So that** {benefit}

**Acceptance Criteria:**
- [ ] {Criterion}

**Priority:** {Priority}
**Story Points:** {Points}

---

## Story Organization Without Epics

If not using epics, organize stories by feature slice or functional area:

## Feature Slice: {Feature Name}

### US-{SLICE}-001: {Story Title}

**As a** {role}
**I want** {capability}
**So that** {benefit}

**Acceptance Criteria:**
- [ ] {Criterion}

**Priority:** {Priority}
**Story Points:** {Points}

---

## Story Status

| Story ID | Title | Priority | Status | Sprint | Story Points |
|----------|-------|----------|--------|--------|--------------|
| US-{ID}-001 | {Title} | High | Completed | Sprint 1 | 5 |
| US-{ID}-002 | {Title} | High | In Progress | Sprint 2 | 3 |
| US-{ID}-003 | {Title} | Medium | Not Started | Backlog | 8 |
| US-{ID}-004 | {Title} | Low | Blocked | - | 5 |

**Total Story Points:** {Total} points
**Completed:** {X} stories, {Y} points
**In Progress:** {X} stories, {Y} points
**Remaining:** {X} stories, {Y} points

---

## **[OPTIONAL]** Traceability Matrix

This section maps user stories to technical components and test coverage for detailed tracking.

### Sprint {N}: {Sprint Name} ({DATE})

| User Story | Technical Component | Status | Test Coverage |
|------------|---------------------|--------|---------------|
| US-{ID}-001 | {package}/feature1/service.py | ✅ Complete | 94% (15 tests) |
| US-{ID}-002 | {package}/feature1/models.py | ✅ Complete | 100% (8 tests) |
| US-{ID}-003 | {package}/feature1/router.py | ⚠️ Partial | 78% (10 tests) |
| US-{ID}-004 | {package}/feature2/service.py | In Progress | N/A |

**Sprint Summary**: X% complete, Y tests passing, Z% overall coverage

### Complete Traceability (All Sprints)

| Story ID | Feature Slice | Service Layer | API Layer | CLI Layer | Test Coverage |
|----------|---------------|---------------|-----------|-----------|---------------|
| US-{ID}-001 | feature1 | ✅ service.py | ✅ router.py | ✅ cli.py | 95% |
| US-{ID}-002 | feature1 | ✅ service.py | N/A | ✅ cli.py | 92% |
| US-{ID}-003 | feature2 | In Progress | Pending | Pending | N/A |

---

## User Story Examples

### Example 1: Simple Feature Story

### US-FT-001: Display Dashboard Summary

**As a** user
**I want** to see a dashboard with key metrics
**So that** I can quickly understand system status

**Acceptance Criteria:**
- [ ] Dashboard displays total active items
- [ ] Dashboard shows items processed today
- [ ] Dashboard includes error rate percentage
- [ ] Dashboard refreshes automatically every 30 seconds
- [ ] Dashboard is accessible from main menu

**Priority:** High
**Story Points:** 3

---

### Example 2: Technical Infrastructure Story

### US-IN-001: Implement Caching Layer

**As a** system
**I want** a caching layer for frequently accessed data
**So that** response times are improved and API calls are reduced

**Acceptance Criteria:**
- [ ] Cache implemented using Redis or in-memory store
- [ ] Cache TTL configurable per data type (default: 1 hour)
- [ ] Cache key includes instance identifier for multi-tenancy
- [ ] Cache invalidation on data updates
- [ ] Cache hit/miss metrics logged
- [ ] Fallback to direct API call on cache failure

**Technical Details:**
- Use Redis for production, in-memory dict for testing
- Implement cache-aside pattern
- Add cache statistics to monitoring dashboard

**Priority:** High
**Story Points:** 8
**Dependencies:** US-IN-002 (Configuration Management)

---

### Example 3: API Integration Story

### US-API-001: Retrieve Resources from External API

**As a** system
**I want** to retrieve resources from the external API
**So that** I can work with current data

**Acceptance Criteria:**
- [ ] System connects to external API using credentials
- [ ] All resources retrieved via API endpoint
- [ ] Response includes all required attributes (ID, name, status, etc.)
- [ ] Invalid or incomplete data is logged and skipped
- [ ] API errors return appropriate error messages
- [ ] Request timeout is configurable (default: 30 seconds)
- [ ] Retry logic with exponential backoff for transient failures

**Technical Details:**
- Use `httpx` for async HTTP requests
- Implement rate limiting (max 10 requests/second)
- Add request/response logging in debug mode
- Cache successful responses for 5 minutes

**Test Strategy:**
- Unit: Mock API responses, test error handling
- Integration: Use saved responses from `tests/saved_responses/`
- E2E: Test complete workflow with mock API server

**Priority:** High
**Story Points:** 5
**Dependencies:** US-IN-001 (Caching Layer)
**Status:** ✅ Completed (2025-11-15)

---

### Example 4: User Interface Story

### US-UI-001: Implement Resource Tree View

**As a** user
**I want** to view resources in a hierarchical tree structure
**So that** I can understand organizational relationships

**Acceptance Criteria:**
- [ ] Tree view displays in left panel of interface
- [ ] Each node shows resource name and type icon
- [ ] Nodes are expandable/collapsible
- [ ] Click on node displays details in right panel
- [ ] Tree state (expansion) persists during session
- [ ] Search/filter functionality for tree nodes
- [ ] Keyboard navigation support (arrow keys, enter)
- [ ] Loading state while fetching tree data
- [ ] Error state with retry option
- [ ] Responsive design for different screen sizes

**Priority:** Medium
**Story Points:** 8

---

### Example 5: Data Processing Story

### US-TR-001: Transform Data Structure

**As a** data engineer
**I want** to transform extracted data into target format
**So that** it can be loaded into destination system

**Acceptance Criteria:**
- [ ] Transform service reads data from previous stage
- [ ] Field mapping applied per configuration
- [ ] Data type conversions validated
- [ ] Calculated fields added based on business rules
- [ ] Invalid records logged with details
- [ ] Transformation summary statistics generated
- [ ] Output data validated against schema
- [ ] Performance: Process 10,000 records in < 5 seconds

**Technical Details:**
- Use Polars for efficient data transformation
- Configuration via `[transform]` section in config.toml
- Support custom transformation functions
- Validate output schema with Pydantic models

**Test Strategy:**
- Unit: Individual transformations, edge cases
- Integration: Full transform pipeline with sample data
- Performance: Benchmark with large datasets

**Priority:** High
**Story Points:** 5
**Status:** ✅ Completed (Sprint 3)

---

## Notes for Developers

### Story Estimation Guidelines

**Story Points Scale:**
- **1 point**: Trivial change (< 1 hour) - config update, documentation fix
- **2 points**: Simple feature (1-2 hours) - small function, basic UI component
- **3 points**: Standard feature (2-4 hours) - service method, API endpoint
- **5 points**: Complex feature (4-8 hours) - full feature slice, integration
- **8 points**: Large feature (1-2 days) - multiple components, complex logic
- **13 points**: Very large (2-3 days) - should be split into smaller stories

**Priority Levels:**
- **High**: Must be completed in assigned sprint, critical for project
- **Medium**: Should be completed, can shift if needed
- **Low**: Nice to have, can be deferred to future sprint

### Definition of Done

A user story is considered "Done" when:

1. **Implementation Complete**
   - [ ] All acceptance criteria met
   - [ ] Code follows project style guidelines
   - [ ] No known bugs or issues

2. **Testing Complete**
   - [ ] Unit tests written and passing (100% of business logic)
   - [ ] Integration tests passing (where applicable)
   - [ ] Manual testing completed
   - [ ] Test coverage meets minimum threshold (90%)

3. **Documentation Complete**
   - [ ] Code documented (docstrings, comments)
   - [ ] User documentation updated (if user-facing)
   - [ ] REQUIREMENTS_TEST_MAPPING.md updated (if applicable)
   - [ ] API documentation updated (if API changes)

4. **Review Complete**
   - [ ] Code reviewed and approved
   - [ ] No unresolved review comments
   - [ ] Technical debt noted and tracked

5. **Acceptance Complete**
   - [ ] Acceptance criteria validated by product owner
   - [ ] No blocking issues or bugs
   - [ ] Story marked as Complete in tracking

### Story Writing Best Practices

**1. User-Centric Language**
- Write from user perspective ("As a...")
- Focus on value ("So that...")
- Avoid technical jargon in story description

**2. Clear Acceptance Criteria**
- Make criteria specific and testable
- Use "Given-When-Then" format when helpful
- Include both functional and non-functional requirements
- Be explicit about error cases and edge conditions

**3. Appropriate Scope**
- Stories should be completable in one sprint
- Stories > 8 points should be split
- Each story should deliver demonstrable value
- Dependencies should be clear

**4. Technical Details**
- Add technical notes for complex stories
- Document architectural decisions
- Include performance requirements
- Reference related documentation

### Dependency Management

**Types of Dependencies:**
- **Technical**: Story requires another story's code
- **Data**: Story needs data/setup from another story
- **Resource**: Story needs specific person/tool
- **External**: Story depends on external team/vendor

**Managing Dependencies:**
- Document all dependencies in story
- Use dependency visualization tools
- Plan stories to minimize blocking
- Communicate blockers immediately
- Update status when dependencies resolve

### Story Refinement Process

**Before Sprint Planning:**
1. Review backlog stories
2. Add acceptance criteria if missing
3. Estimate story points as team
4. Identify and resolve dependencies
5. Split large stories (> 8 points)
6. Clarify ambiguities with stakeholders

**During Sprint:**
1. Review acceptance criteria at story start
2. Create tasks if needed
3. Update story status regularly
4. Document blockers immediately
5. Validate with stakeholders before marking done

### Tracking and Reporting

**Story Status Values:**
- **Not Started**: Story not yet begun
- **In Progress**: Active development
- **Blocked**: Waiting on dependency or decision
- **In Review**: Code review or testing
- **Completed**: All Done criteria met
- **Archived**: Moved to sprint archive document

**Velocity Tracking:**
- Track completed story points per sprint
- Calculate average velocity over 3 sprints
- Use velocity for sprint planning
- Adjust estimates based on actuals

### Story Categories by Type

**Feature Stories** (User-facing functionality)
- New capabilities users can access
- UI enhancements
- User workflow improvements

**Technical Stories** (Infrastructure/Architecture)
- Performance optimization
- Refactoring
- Technical debt reduction
- Infrastructure setup

**Bug Stories** (Defect fixes)
- Production bugs
- Regression issues
- Data quality problems

**Spike Stories** (Research/Investigation)
- Technology evaluation
- Proof of concept
- Design exploration
- Time-boxed investigation

---

## Continuous Improvement

This document is a living artifact. Update it as:
- New requirements are discovered
- User stories are refined or split
- Implementation reveals better approaches
- User feedback suggests improvements
- Team retrospectives identify process changes

**Review Frequency:**
- **Daily**: Update story status
- **Weekly**: Groom backlog, refine upcoming stories
- **Sprint End**: Archive completed stories, update totals
- **Monthly**: Review overall story quality and patterns

---

**Template Version:** 1.0.0
**Last Updated:** {DATE}
