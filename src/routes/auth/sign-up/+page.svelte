<script lang="ts">
	import { Button } from '$lib/components/ui/button/index.js';
	import * as Card from '$lib/components/ui/card/index.js';
	import * as Field from '$lib/components/ui/field/index.js';
	import { Input } from '$lib/components/ui/input/index.js';
	import type { ComponentProps } from 'svelte';
	import { authClient } from '$lib/auth-client';
	import { api } from '$convex/_generated/api';
	import { useQuery } from 'convex-svelte';
	import { useAuth } from '@mmailaender/convex-better-auth-svelte/svelte';
	import { type BUTTON_STATES } from '$lib/schemas-and-types/types';

	let { data, ...restProps }: { data: any } & ComponentProps<typeof Card.Root> = $props();

	// Auth Stuff
	const auth = useAuth();
	const isLoading = $derived(auth.isLoading && !data.currentUser);
	const isAuthenticated = $derived(auth.isAuthenticated || !!data.currentUser);

	const currentUserResponse = useQuery(api.auth.getCurrentUser, () =>
		isAuthenticated ? {} : 'skip'
	);
	let user = $derived(currentUserResponse.data ?? data.currentUser);

	// Auth variables
	let name = $state('');
	let email = $state('');
	let password = $state('');

	// Auth submission
	async function handlePasswordSubmit(event: Event) {
		event.preventDefault();
		try {
			await authClient.signUp.email(
				{ name, email, password },
				{
					onError: (ctx) => {
						alert(ctx.error.message);
					}
				}
			);
		} catch (error) {
			console.error('Authentication error:', error);
		}
	};

	
</script>

<Card.Root {...restProps} class="w-full max-w-sm">
	<Card.Header>
		<Card.Title>Create an account</Card.Title>
		<Card.Description>Enter your information below to create your account</Card.Description>
	</Card.Header>
	<Card.Content>
		<form onsubmit={handlePasswordSubmit}>
			<Field.Group>
				<Field.Field>
					<Field.Label for="name">Full Name</Field.Label>
					<Input id="name" type="text" placeholder="John Doe" required bind:value={name} />
				</Field.Field>
				<Field.Field>
					<Field.Label for="email">Email</Field.Label>
					<Input id="email" type="email" placeholder="m@example.com" required bind:value={email} />
					<Field.Description>
						We'll use this to contact you. We will not share your email with anyone else.
					</Field.Description>
				</Field.Field>
				<Field.Field>
					<Field.Label for="password">Password</Field.Label>
					<Input id="password" type="password" required bind:value={password} />
					<Field.Description>Must be at least 8 characters long.</Field.Description>
				</Field.Field>
				<Field.Group>
					<Field.Field>
						<Button type="submit">Create Account</Button>
						<Field.Description class="px-6 text-center">
							Already have an account? <a href="/auth/sign-in">Sign in</a>
						</Field.Description>
					</Field.Field>
				</Field.Group>
			</Field.Group>
		</form>
	</Card.Content>
</Card.Root>
