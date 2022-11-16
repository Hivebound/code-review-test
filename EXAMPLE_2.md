# Code Review Example 2

```typescript
it('should return records based on the limit', async () => {
  await createBookingFixture(request, facility._id, {
    ramp_assigned: 1,
  })
  await createBookingFixture(request, facility._id, {
    ramp_assigned: 2,
  })

  const res = await request.get(`/api/calendar/facilities/${facility._id}/bookings?limit=2`)

  expect(res.status).toEqual(200)
  expect(res.body).toMatchObject({ page: 1, limit: 2 })
})
```
