ReFrame — CEAS Co-op Reflection System
Copyright (c) 2026 Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha,
Mbathio Lo

Licensed under the MIT License. See LICENSE.

================================================================================
THIRD-PARTY DEPENDENCIES
================================================================================

This product includes software developed by third parties, listed below with
the license each is distributed under.

Status: this is the PLANNED stack, taken from the D1 build-versus-reuse table.
No manifest file exists yet. Regenerate this list from the real package.json and
requirements.txt once code is committed.

A checkmark means the license was confirmed against a primary source on the date
shown. Everything else is still to be confirmed by opening the project's own
LICENSE file -- not from memory.

--------------------------------------------------------------------------------
Backend
--------------------------------------------------------------------------------

PostgreSQL                      PostgreSQL        https://www.postgresql.org
Python 3                        PSF-2.0           https://www.python.org
FastAPI                         MIT               https://github.com/fastapi/fastapi
Pydantic                        MIT               https://github.com/pydantic/pydantic
SQLAlchemy                      MIT               https://github.com/sqlalchemy/sqlalchemy
Alembic                         MIT               https://github.com/sqlalchemy/alembic
httpx                           BSD-3-Clause      https://github.com/encode/httpx
pytest                          MIT               https://github.com/pytest-dev/pytest

psycopg 3                       LGPL-3.0-only     https://github.com/psycopg/psycopg
    [confirmed 2026-11-08: conda-forge and the OpenEmbedded recipe both state
     LGPL-3.0-only; Debian's copyright file states LGPL-3]

    THE ONLY COPYLEFT DEPENDENCY IN THIS PROJECT.
    ReFrame uses psycopg as an unmodified dependency, which the LGPL permits.
    The source-disclosure obligation attaches to psycopg itself, not to
    ReFrame's own code. If psycopg is ever vendored or patched, that changes.
    asyncpg (Apache-2.0) is the fallback if a non-LGPL driver is preferred.

Authlib (if OIDC)               BSD-3-Clause      https://github.com/authlib/authlib
    [confirmed 2026-11-08 against Authlib's own licenses page]
    MUST be pinned to v0.11 or later. Earlier versions were AGPL-3.0, which
    would put a strong copyleft license into this stack.

python3-saml (if SAML)          MIT               https://github.com/SAML-Toolkits/python3-saml
    [confirmed 2026-11-08: Debian copyright for python3-onelogin-saml2 states
     MIT for all upstream files]
    Upstream moved from onelogin/ to SAML-Toolkits/. Bundled assets carry
    W3C-Software and PSF licenses.

Only one of Authlib or python3-saml will be used. The choice depends on whether
UC supports OpenID Connect or SAML -- open with UC IT as of 2026-11-10.

--------------------------------------------------------------------------------
Frontend
--------------------------------------------------------------------------------

React                           MIT               https://github.com/facebook/react
Vite                            MIT               https://github.com/vitejs/vite

--------------------------------------------------------------------------------
Obligations these place on us
--------------------------------------------------------------------------------

MIT, BSD-3-Clause, PSF-2.0, PostgreSQL:
    Retain the copyright notice and license text in any distribution.
    BSD-3-Clause additionally forbids using contributor names to endorse this
    project without permission.

LGPL-3.0-only (psycopg 3):
    See the note above. Compatible with ReFrame's MIT license for use as an
    unmodified dependency.

All of the above are compatible with ReFrame's own MIT license.

================================================================================
DATASETS
================================================================================

None. ReFrame's inputs are student reflections, supervisor evaluations and
university records. No external dataset is used.

Per CON-06, development uses synthetic data until institutional approval.

================================================================================
NON-CODE ASSETS
================================================================================

None as of 2026-11-10. Any icon, font or image added to the web front end must
be listed here with its author and license.

================================================================================
CODE ADAPTED FROM OTHER SOURCES
================================================================================

None recorded as of 2026-11-10. Every team member must add any snippet they
adapt from a tutorial or another repository, with the URL it came from, as code
is written.

================================================================================
EXTERNAL SERVICES
================================================================================

E1  University sign-in. Authentication is delegated entirely; no passwords are
    stored by ReFrame.
E2  University records. Enrollment, placements, advisor and supervisor
    assignments.
E3  Model provider for adaptive prompts. NOT YET CHOSEN. When selected, record
    it here along with its data-handling terms.
